# Blueprint: E-commerce Operations Manager — Daily Order Exception & Inventory Reconciliation Report

## Role Profile

| Field | Detail |
|-------|--------|
| **Role** | E-commerce Operations Manager |
| **Industry** | Retail / Direct-to-Consumer / Multi-channel Commerce |
| **Company Size** | Small to mid-size (50–500 SKUs, 100–5,000 orders/day) |
| **Time Saved** | 12–15 hours per week |
| **Complexity** | Medium |
| **Tools Required** | Shopify/WooCommerce API, Google Sheets, Email (SMTP), Python (or n8n/Make), Claude API |

---

## The Problem

E-commerce Operations Managers spend the first 2–3 hours of every day on the same cycle:

1. **Order exception identification** — hunting through order dashboards for failed payments, address issues, inventory stockouts that caused partial fulfillment, and shipping label errors
2. **Multi-channel inventory reconciliation** — comparing stock levels across Shopify, Amazon, Walmart Marketplace, and the warehouse management system to find discrepancies before oversells happen
3. **Returns & refund queue triage** — reviewing return requests, categorizing reasons, approving/denying based on policy, and updating inventory counts
4. **Daily performance email** — manually compiling order volume, revenue, fulfillment rates, return rates, and flagging issues for the team

This is 10–15 hours/week of reactive, repetitive work that delays strategic decisions about merchandising, promotions, and supplier negotiations.

---

## The Automated Workflow

### Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DAILY TRIGGER (6:00 AM)                          │
└─────────────────────┬───────────────────────────────────────────────┘
                      │
          ┌───────────┼───────────────┐
          ▼           ▼               ▼
┌─────────────┐ ┌───────────┐ ┌──────────────┐
│  ORDER      │ │ INVENTORY │ │  RETURNS     │
│  EXCEPTION  │ │ RECONCILE │ │  TRIAGE      │
│  SCANNER    │ │ ENGINE    │ │  ENGINE      │
└──────┬──────┘ └─────┬─────┘ └──────┬───────┘
       │               │              │
       │    ┌──────────┴──────────┐   │
       │    │  DISCREPANCY        │   │
       │    │  RESOLVER           │   │
       │    └──────────┬──────────┘   │
       │               │              │
       ▼               ▼              ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   CLAUDE AI ANALYSIS ENGINE                          │
│                                                                     │
│  • Classify exceptions by severity & root cause                     │
│  • Identify inventory drift patterns                                │
│  • Auto-approve standard returns / flag edge cases                  │
│  • Generate natural-language executive summary                      │
└─────────────────────────────┬───────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
     ┌────────────┐  ┌──────────────┐  ┌─────────────┐
     │ EXCEPTION  │  │ INVENTORY    │  │ TEAM EMAIL  │
     │ DASHBOARD  │  │ ADJUSTMENT   │  │ REPORT +    │
     │ (Sheets)   │  │ QUEUE        │  │ SLACK ALERT │
     └────────────┘  └──────────────┘  └─────────────┘
```

---

## Implementation Steps

### Module 1: Order Exception Scanner

**What it does:** Pulls all orders from the last 24 hours, identifies exceptions (failed payments, address validation failures, out-of-stock holds, shipping label errors), and classifies severity.

**Prompt Template:**

```
You are an e-commerce operations analyst. Analyze the following batch of orders and identify all exceptions.

For each exception, provide:
- Order ID
- Customer name & email
- Exception type: [payment_failed | address_invalid | partial_stockout | label_error | fraud_flag | duplicate_order]
- Severity: [critical | high | medium | low]
- Recommended action
- Customer communication needed: [yes/no]
- Draft customer email (if yes)

Orders data:
{{orders_json}}

Rules:
- Payment failures over $200 are CRITICAL
- Address issues in international orders are HIGH
- Partial stockouts are HIGH if the backordered item is the primary purchase
- Duplicate orders within 5 minutes from same customer are likely accidental — flag as MEDIUM
- Any order with 3+ fraud indicators is CRITICAL (flag for manual review, do NOT auto-cancel)

Output as structured JSON with a summary section at the top.
```

**Code Example (Python):**

```python
import requests
import json
from datetime import datetime, timedelta
from anthropic import Anthropic

# Shopify API — pull last 24h orders
def fetch_recent_orders(shop_url, access_token):
    yesterday = (datetime.now() - timedelta(days=1)).isoformat()
    headers = {"X-Shopify-Access-Token": access_token}
    url = f"https://{shop_url}/admin/api/2024-01/orders.json"
    params = {
        "created_at_min": yesterday,
        "status": "any",
        "limit": 250
    }
    
    all_orders = []
    while url:
        response = requests.get(url, headers=headers, params=params)
        data = response.json()
        all_orders.extend(data.get("orders", []))
        # Handle pagination
        link_header = response.headers.get("Link", "")
        url = None
        if 'rel="next"' in link_header:
            url = link_header.split(";")[0].strip("<>")
        params = {}
    
    return all_orders

# Identify exceptions
def scan_for_exceptions(orders):
    exceptions = []
    for order in orders:
        issues = []
        
        # Payment failures
        if order.get("financial_status") in ["pending", "voided", "refunded"]:
            issues.append("payment_failed")
        
        # Address validation
        shipping = order.get("shipping_address", {})
        if not shipping.get("zip") or not shipping.get("city"):
            issues.append("address_invalid")
        
        # Stockout check (fulfillment status)
        if order.get("fulfillment_status") == "partial":
            issues.append("partial_stockout")
        
        # Fraud indicators
        fraud_score = 0
        if order.get("buyer_accepts_marketing") == False and float(order.get("total_price", 0)) > 500:
            fraud_score += 1
        if shipping.get("country_code") != order.get("billing_address", {}).get("country_code"):
            fraud_score += 1
        if order.get("browser_ip") is None:
            fraud_score += 1
        if fraud_score >= 3:
            issues.append("fraud_flag")
        
        if issues:
            exceptions.append({
                "order_id": order["id"],
                "order_number": order.get("order_number"),
                "customer": order.get("customer", {}).get("email", "unknown"),
                "total": order.get("total_price"),
                "issues": issues,
                "created_at": order.get("created_at")
            })
    
    return exceptions

# Claude analysis
def analyze_exceptions(exceptions):
    client = Anthropic()
    
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=4000,
        messages=[{
            "role": "user",
            "content": f"""Analyze these e-commerce order exceptions and provide:
1. Severity classification for each
2. Recommended resolution action
3. Draft customer emails where needed
4. Pattern analysis (are multiple exceptions related?)

Exceptions:
{json.dumps(exceptions, indent=2)}

Respond in JSON format with a 'summary' object and 'exceptions' array."""
        }]
    )
    
    return json.loads(response.content[0].text)
```

---

### Module 2: Multi-Channel Inventory Reconciliation

**What it does:** Compares inventory counts across all sales channels and the warehouse system. Identifies discrepancies, calculates drift rate, and recommends adjustments.

**Prompt Template:**

```
You are an inventory reconciliation specialist. Compare the following inventory snapshots from multiple channels and identify discrepancies.

Channel data:
- Shopify: {{shopify_inventory}}
- Amazon FBA: {{amazon_inventory}}
- Warehouse (WMS): {{wms_inventory}}

For each SKU with a discrepancy:
1. Identify which channel is out of sync
2. Calculate the variance (units and %)
3. Determine likely cause: [oversell_risk | sync_delay | manual_adjustment_missed | return_not_processed | shrinkage]
4. Recommend action: [adjust_channel | investigate | hold_sales | recount]
5. Priority: [urgent | today | this_week]

Flag any SKU where available-to-sell across all channels exceeds WMS physical count — these are URGENT oversell risks.

Also provide:
- Total SKUs checked
- SKUs with discrepancies (count and %)
- Top 5 highest-risk items
- Trend note if this SKU has had discrepancies 3+ days in a row
```

**Code Example (Python):**

```python
import pandas as pd

def reconcile_inventory(shopify_inv, amazon_inv, wms_inv):
    """
    Each input is a dict: {sku: {"available": int, "reserved": int}}
    """
    all_skus = set(list(shopify_inv.keys()) + list(amazon_inv.keys()) + list(wms_inv.keys()))
    
    discrepancies = []
    for sku in all_skus:
        shopify_qty = shopify_inv.get(sku, {}).get("available", 0)
        amazon_qty = amazon_inv.get(sku, {}).get("available", 0)
        wms_qty = wms_inv.get(sku, {}).get("available", 0)
        
        total_listed = shopify_qty + amazon_qty
        
        # Oversell risk: listed across channels > actual warehouse stock
        if total_listed > wms_qty:
            discrepancies.append({
                "sku": sku,
                "shopify": shopify_qty,
                "amazon": amazon_qty,
                "wms": wms_qty,
                "total_listed": total_listed,
                "variance": total_listed - wms_qty,
                "risk": "oversell",
                "priority": "urgent"
            })
        # Sync drift: individual channel doesn't match allocation
        elif abs(shopify_qty - wms_inv.get(sku, {}).get("shopify_allocated", 0)) > 2:
            discrepancies.append({
                "sku": sku,
                "shopify": shopify_qty,
                "amazon": amazon_qty,
                "wms": wms_qty,
                "variance": shopify_qty - wms_inv.get(sku, {}).get("shopify_allocated", 0),
                "risk": "sync_drift",
                "priority": "today"
            })
    
    return discrepancies

def generate_adjustment_queue(discrepancies, client):
    """Use Claude to prioritize and create actionable adjustment tasks"""
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=3000,
        messages=[{
            "role": "user",
            "content": f"""Given these inventory discrepancies, create a prioritized 
adjustment queue. For each item, specify the exact adjustment to make in each system.

Discrepancies:
{json.dumps(discrepancies, indent=2)}

Format as a numbered action list, urgent items first. Include the specific 
API call or manual step needed for each adjustment."""
        }]
    )
    return response.content[0].text
```

---

### Module 3: Returns & Refund Triage Engine

**What it does:** Processes the return request queue, auto-approves standard returns that meet policy, flags edge cases for human review, and updates inventory availability.

**Prompt Template:**

```
You are a returns processing specialist. Evaluate each return request against our policy and decide: auto-approve, escalate, or deny.

Return Policy Rules:
- Within 30 days of delivery: full refund eligible
- 31-60 days: store credit only
- Opened electronics: 15-day window, must include all accessories
- Clothing: unworn with tags, no exceptions
- "Changed my mind" after 30 days: deny with polite explanation
- Damaged in transit: always approve + free return shipping
- Wrong item sent: always approve + free return shipping + expedite replacement
- Items over $500: require manager approval regardless of reason

Return Requests:
{{returns_queue}}

For each request provide:
- Decision: [auto_approve_refund | auto_approve_credit | escalate_manager | deny]
- Reason code
- Customer communication (empathetic, brand-voice)
- Inventory action: [return_to_stock | inspect_first | damage_write_off]
- Replacement needed: [yes/no]
- Estimated refund amount

Also flag any patterns (same customer returning frequently, same product being returned often — potential quality issue).
```

---

### Module 4: Daily Report Generator

**What it does:** Compiles all findings into an executive-ready email report with key metrics, action items, and trend analysis.

**Report Template:**

```
You are composing a daily operations report for an e-commerce team. Use the following data to create a clear, scannable report.

Today's Data:
- Orders processed: {{order_count}}
- Revenue: {{revenue}}
- Exceptions found: {{exception_count}}
- Exceptions by type: {{exception_breakdown}}
- Inventory discrepancies: {{discrepancy_count}}
- Oversell risks: {{oversell_risks}}
- Returns processed: {{returns_processed}}
- Returns auto-approved: {{auto_approved}}
- Returns escalated: {{escalated}}
- Returns denied: {{denied}}

Format the report with:
1. Executive Summary (3 sentences max — what happened, what needs attention, what's trending)
2. Key Metrics table (today vs yesterday vs 7-day avg)
3. Action Required section (numbered list, owner assigned, deadline)
4. Wins section (positive trends, efficiency gains)
5. Watch List (emerging issues that aren't critical yet)

Tone: professional but direct. No fluff. Bold the numbers that are significantly above/below normal.
```

---

## Example Output: Daily Report

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 DAILY E-COMMERCE OPS REPORT — May 5, 2026
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

EXECUTIVE SUMMARY
Strong order volume (+12% vs 7-day avg) with 23 exceptions 
requiring attention. Two urgent oversell risks on SKUs 
WIDGET-BLK-L and CABLE-USB-C need immediate channel adjustment. 
Returns rate holding steady at 4.2%.

━━━ KEY METRICS ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
                    Today    Yesterday   7-Day Avg
Orders:              847        756        752
Revenue:          $67,240    $58,320    $59,100
Fulfillment Rate:  96.8%      97.2%      97.0%
Exceptions:          23         18         16
Returns Filed:       36         32         31
Auto-Approved:       28         25         24
Avg Resolution:    1.2hr      1.8hr      2.1hr

━━━ ACTION REQUIRED ━━━━━━━━━━━━━━━━━━━━━━━━━━━━
1. [URGENT] Adjust inventory for WIDGET-BLK-L
   → Shopify shows 45 available, WMS shows 12
   → Pause Shopify listing NOW, adjust to 6 (reserve for Amazon)
   Owner: Inventory Team | Deadline: 9:00 AM

2. [URGENT] Adjust inventory for CABLE-USB-C  
   → Amazon shows 200, actual count 87
   → Update Amazon listing, investigate 113-unit gap
   Owner: Amazon Channel Mgr | Deadline: 9:00 AM

3. [HIGH] 5 orders with invalid shipping addresses (intl)
   → Customer emails drafted and queued for review
   → 3 are to new postal codes in Brazil (carrier may not deliver)
   Owner: Customer Service | Deadline: 11:00 AM

4. [MEDIUM] Escalated returns requiring manager approval
   → 3 electronics returns over $500
   → Details in Returns Dashboard tab
   Owner: Returns Manager | Deadline: EOD

━━━ WINS ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✓ Auto-approval rate: 78% (up from 71% last week)
✓ Customer response drafts saved est. 2.3 hours today
✓ Zero oversells executed (caught 2 before customer orders)

━━━ WATCH LIST ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠ SKU HOODIE-GRY-M returned 8 times in 5 days (sizing issue?)
⚠ Payment failure rate up 0.3% — possible gateway issue
⚠ Amazon sync latency increased to 45min (normally 15min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Implementation Timeline

### Week 1: Foundation
| Day | Task |
|-----|------|
| Mon | Set up API connections (Shopify, Amazon SP-API, WMS) |
| Tue | Build order exception scanner, test with 1 day of data |
| Wed | Build inventory reconciliation module |
| Thu | Build returns triage engine with policy rules |
| Fri | Build report generator, test full pipeline end-to-end |

### Week 2: Automation & Refinement
| Day | Task |
|-----|------|
| Mon | Set up scheduled trigger (cron/n8n/Make) for 6:00 AM daily |
| Tue | Add Slack webhook for urgent oversell alerts |
| Wed | Connect Google Sheets for exception dashboard |
| Thu | Tune Claude prompts based on first week's accuracy |
| Fri | Add historical trend tracking (7-day rolling comparisons) |

### Week 3: Advanced Features
| Day | Task |
|-----|------|
| Mon | Add duplicate order detection and auto-merge |
| Tue | Add customer lifetime value context to returns decisions |
| Wed | Build weekly summary roll-up for leadership |
| Thu | Add carrier performance tracking to shipment exceptions |
| Fri | Document SOPs for team, train backup operator |

---

## Cost Estimate

| Component | Monthly Cost |
|-----------|-------------|
| Claude API (Sonnet, ~500 calls/day) | $45–80 |
| Shopify API | Included in plan |
| Amazon SP-API | Free |
| n8n/Make automation platform | $20–50 |
| Google Sheets (storage) | Free |
| **Total** | **$65–130/month** |

**ROI Calculation:**
- Time saved: 12–15 hours/week × $45/hr (ops manager salary) = $2,160–2,700/month
- Oversell prevention: Avg 3 prevented/week × $85 (avg cost of oversell resolution) = $1,020/month
- Faster returns processing: Improved customer satisfaction → estimated 2% reduction in churn
- **Net monthly value: $3,000–3,800 vs. $65–130 cost = 23–58x ROI**

---

## Customization Options

### For Higher Volume Stores (5,000+ orders/day)
- Swap Google Sheets for a proper database (PostgreSQL/Supabase)
- Add batch processing with pagination for API calls
- Implement real-time webhooks instead of daily batch scan
- Add dedicated fraud ML model alongside Claude analysis

### For Multi-Warehouse Operations
- Add warehouse-specific inventory tracking
- Include transfer order recommendations between locations
- Add fulfillment routing optimization (ship from nearest warehouse)

### For Subscription/Recurring Revenue Businesses
- Add subscription renewal failure detection
- Include MRR impact calculations in exception reports
- Add churn risk scoring based on return/complaint patterns

---

## No-Code Alternative (n8n / Make.com)

For teams that prefer visual workflow builders:

```
Trigger: Schedule (6:00 AM daily)
    │
    ├── HTTP Request → Shopify Orders API (last 24h)
    ├── HTTP Request → Amazon SP-API Inventory
    ├── HTTP Request → WMS API Inventory Levels
    │
    ├── Code Node → Identify exceptions & discrepancies
    │
    ├── Claude Node → Analyze exceptions, draft emails, triage returns
    │
    ├── Google Sheets → Log exceptions + adjustments
    ├── Gmail → Send daily report to team
    └── Slack → Post urgent alerts to #ops-alerts channel
```

Each module can be built as a separate workflow and chained together, making it easy to debug and modify individual components without breaking the full pipeline.

---

## Success Metrics

Track these KPIs to measure the workflow's impact:

| Metric | Before | Target (30 days) | Target (90 days) |
|--------|--------|-------------------|-------------------|
| Morning ops review time | 2.5 hrs | 30 min | 15 min |
| Oversells per week | 3–5 | 0–1 | 0 |
| Return processing time | 4–6 hrs | 1 hr | 30 min |
| Exception resolution time | Same-day | 2 hours | 1 hour |
| Inventory accuracy | 94% | 98% | 99.5% |
| Customer satisfaction (returns) | 3.2/5 | 4.0/5 | 4.5/5 |

---

## Failure Modes & Safeguards

| Risk | Mitigation |
|------|-----------|
| API rate limits hit | Implement exponential backoff + cache last known good data |
| Claude misclassifies exception | Human review queue for first 2 weeks; confidence threshold (flag if <85%) |
| Inventory data stale | Timestamp all data; alert if any source is >2 hours old |
| Returns auto-approved incorrectly | Cap auto-approval at $200 for first month; audit 10% sample daily |
| Email report not sent | Dead man's switch: if no report by 7:00 AM, alert ops manager via SMS |

---

## Getting Started Checklist

- [ ] Verify API access to all sales channels (Shopify, Amazon, etc.)
- [ ] Export one day of orders and inventory as JSON for testing
- [ ] Set up Claude API key with appropriate spend limits
- [ ] Create Google Sheet with tabs: Exceptions, Inventory, Returns, Daily Metrics
- [ ] Build and test Module 1 (Order Exceptions) with historical data
- [ ] Build and test Module 2 (Inventory Reconciliation)
- [ ] Build and test Module 3 (Returns Triage)
- [ ] Build and test Module 4 (Report Generator)
- [ ] Set up automation trigger (cron job or n8n/Make)
- [ ] Run in "shadow mode" for 3 days (generate report but don't auto-act)
- [ ] Compare automated output to manual process for accuracy
- [ ] Go live with daily reports; enable auto-actions after 1 week confidence

---

*Blueprint created: May 5, 2026 | Category: E-commerce & Retail Operations*
*Estimated implementation time: 2–3 weeks | Difficulty: Medium*
