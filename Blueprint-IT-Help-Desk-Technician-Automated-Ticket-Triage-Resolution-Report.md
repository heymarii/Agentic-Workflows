# Blueprint: IT Help Desk Technician — Automated Ticket Triage & Resolution Report

**Role:** IT Help Desk Technician (Tier 1 / Tier 2 Support)  
**Task Automated:** Incoming ticket classification, known-fix matching, auto-response drafting, and daily resolution metrics reporting  
**Time Saved:** ~10–14 hours per week (2–3 hours daily)  
**Difficulty to Implement:** Low to Medium  
**Tools Required:** Ticketing system (Jira Service Management, Zendesk, Freshdesk, ServiceNow, etc.), internal knowledge base/wiki, email or Slack, AI assistant (Claude or GPT), Python or n8n/Make for orchestration

---

## The Problem

An IT help desk technician at a mid-sized company (200–2,000 employees) handles 40–80 tickets per day. The vast majority — roughly 60–70% — are repetitive issues the team has solved dozens of times before: password resets, VPN connection failures, printer setup, software installation requests, email configuration, and permission access requests.

Despite having a knowledge base, the technician still manually reads every ticket, mentally classifies its category and urgency, searches for a relevant article or past resolution, drafts a response or walks the user through a fix, and updates the ticket status. When tickets pile up during peak hours (Monday mornings, after software rollouts), response times balloon and user satisfaction drops.

**The manual workflow typically looks like this:**

1. Open the ticket queue and scan new incoming tickets (10 min)
2. Read each ticket, determine category and priority level (30 min for 50 tickets)
3. Search the knowledge base for matching resolution articles (20 min)
4. Draft a personalized response with step-by-step instructions (40 min)
5. Escalate tickets that require Tier 2/Tier 3 attention (10 min)
6. Update ticket fields — category, priority, assignee, status (15 min)
7. Compile end-of-day metrics for the team lead — tickets resolved, average response time, open backlog, escalation count (20 min)

**Total: ~2.5 hours every single day on work that is almost entirely pattern-matching.**

The real cost isn't just the technician's time — it's the compounding effect of slow responses. Every hour a ticket sits unacknowledged, the end user is either stuck (productivity loss) or submitting duplicate tickets (more queue noise). A 15-minute delay in classifying a critical infrastructure ticket can cascade into a company-wide outage.

---

## The Automated Workflow

### Overview

This workflow intercepts every new ticket the moment it's submitted, uses AI to classify it by category and urgency, matches it against your internal knowledge base for known resolutions, drafts a personalized response ready for the technician to review and send, and compiles a real-time dashboard plus a daily summary report — all without the technician touching the queue.

The technician's role shifts from "read-classify-search-draft" to "review-approve-send." For the 60–70% of tickets with known fixes, that cuts per-ticket handling time from 3–5 minutes down to 30 seconds.

### Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                     TRIGGER: NEW TICKET CREATED                  │
│           (Webhook from Jira / Zendesk / Freshdesk / ServiceNow) │
└───────────────────────────┬──────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────────────┐
│                STEP 1: TICKET INTAKE & ENRICHMENT                │
│                                                                  │
│  • Extract ticket subject, description, and submitter info       │
│  • Pull submitter's device/OS from asset management (if avail.)  │
│  • Check for duplicate/related open tickets from same user       │
│  • Normalize free-text into structured fields                    │
│                                                                  │
└───────────────────────────┬──────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────────────┐
│              STEP 2: AI CLASSIFICATION ENGINE                    │
│                                                                  │
│  ┌─────────────────────┐   ┌──────────────────────────────────┐  │
│  │  CATEGORY ASSIGNMENT │   │  PRIORITY SCORING                │  │
│  │                      │   │                                  │  │
│  │  • Password/Access   │   │  P1 Critical: system-wide outage │  │
│  │  • Network/VPN       │   │  P2 High: team-blocking issue    │  │
│  │  • Hardware          │   │  P3 Medium: single-user impact   │  │
│  │  • Software Install  │   │  P4 Low: request / how-to        │  │
│  │  • Email/Calendar    │   │                                  │  │
│  │  • Printer/Peripheral│   │  Factors: # users affected,      │  │
│  │  • Security Incident │   │  business criticality keywords,  │  │
│  │  • New Hire Setup    │   │  submitter role/department        │  │
│  │  • Other             │   │                                  │  │
│  └─────────────────────┘   └──────────────────────────────────┘  │
│                                                                  │
│  Confidence score attached to every classification               │
│  If confidence < 75%, flag for human review instead of auto-route│
│                                                                  │
└───────────────────────────┬──────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────────────┐
│           STEP 3: KNOWLEDGE BASE MATCHING                        │
│                                                                  │
│  • Semantic search against internal KB articles                  │
│  • Match against past resolved tickets (last 90 days)            │
│  • Rank top 3 resolution candidates by relevance score           │
│  • If top match > 90% confidence → auto-draft response           │
│  • If 70–90% → suggest to technician with "likely match" tag     │
│  • If < 70% → route to manual investigation                     │
│                                                                  │
└───────────────────────────┬──────────────────────────────────────┘
                            │
                ┌───────────┴───────────┐
                │                       │
                ▼                       ▼
┌──────────────────────────┐ ┌─────────────────────────────────┐
│  STEP 4A: AUTO-RESPONSE  │ │  STEP 4B: ESCALATION ROUTING    │
│                          │ │                                 │
│  • Draft personalized    │ │  • P1/P2 → page on-call + Slack │
│    response using KB     │ │  • Security → Security team     │
│    article + user context│ │  • Hardware → Facilities/vendor │
│  • Include step-by-step  │ │  • Low-confidence → senior tech │
│    instructions          │ │  • Attach context packet for    │
│  • Add self-service links│ │    receiving team               │
│  • Queue for tech review │ │                                 │
│    (1-click approve/edit)│ │                                 │
└──────────────────────────┘ └─────────────────────────────────┘
                │                       │
                └───────────┬───────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────────────┐
│              STEP 5: DAILY METRICS & REPORTING                   │
│                (Scheduled: end of business day)                   │
│                                                                  │
│  ┌──────────────────┐  ┌────────────────┐  ┌─────────────────┐  │
│  │ Resolution Report │  │ Trend Analysis │  │ Team Dashboard  │  │
│  │                   │  │                │  │                 │  │
│  │ • Tickets opened  │  │ • Top 5 issue  │  │ • Real-time     │  │
│  │ • Tickets closed  │  │   categories   │  │   queue view    │  │
│  │ • Avg response    │  │ • Repeat       │  │ • Per-tech      │  │
│  │   time            │  │   offenders    │  │   workload      │  │
│  │ • Avg resolution  │  │   (users with  │  │ • SLA countdown │  │
│  │   time            │  │   5+ tickets)  │  │   timers        │  │
│  │ • SLA compliance  │  │ • Emerging     │  │ • Escalation    │  │
│  │   rate            │  │   patterns     │  │   tracker       │  │
│  │ • Auto-resolved % │  │                │  │                 │  │
│  └──────────────────┘  └────────────────┘  └─────────────────┘  │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Implementation Details

### Step 1: Ticket Intake Webhook Handler

This script listens for new ticket webhooks from your ticketing system, extracts the relevant fields, and passes them to the classification engine. Example using Jira Service Management webhooks:

```python
from flask import Flask, request, jsonify
import requests
import json
from datetime import datetime

app = Flask(__name__)

# ── Configuration ──
JIRA_BASE_URL = "https://your-domain.atlassian.net"
JIRA_API_TOKEN = "your-api-token"
JIRA_EMAIL = "helpdesk@yourcompany.com"
AI_API_KEY = "your-claude-api-key"
KB_SEARCH_ENDPOINT = "https://your-kb-api.com/search"

@app.route("/webhook/new-ticket", methods=["POST"])
def handle_new_ticket():
    """Receive webhook when a new ticket is created in Jira Service Management."""
    payload = request.json
    
    # Extract core ticket data
    ticket = {
        "id": payload["issue"]["key"],
        "summary": payload["issue"]["fields"]["summary"],
        "description": payload["issue"]["fields"].get("description", ""),
        "reporter_email": payload["issue"]["fields"]["reporter"]["emailAddress"],
        "reporter_name": payload["issue"]["fields"]["reporter"]["displayName"],
        "created": payload["issue"]["fields"]["created"],
        "components": [c["name"] for c in payload["issue"]["fields"].get("components", [])],
    }
    
    # Enrich with user context from asset management
    ticket["user_context"] = get_user_context(ticket["reporter_email"])
    
    # Check for duplicate tickets
    ticket["possible_duplicates"] = check_duplicates(
        ticket["reporter_email"], ticket["summary"]
    )
    
    # Send to classification engine
    classification = classify_ticket(ticket)
    
    # Route based on classification
    route_ticket(ticket, classification)
    
    return jsonify({"status": "processed", "ticket_id": ticket["id"]}), 200


def get_user_context(email):
    """Pull user's device info and ticket history from asset management."""
    # Example: query your CMDB or asset management API
    # Returns device type, OS, department, VIP status, recent ticket count
    try:
        response = requests.get(
            f"{JIRA_BASE_URL}/rest/api/3/user/search?query={email}",
            auth=(JIRA_EMAIL, JIRA_API_TOKEN),
        )
        user_data = response.json()
        
        # Get recent ticket count for this user (last 30 days)
        jql = f'reporter = "{email}" AND created >= -30d'
        tickets_response = requests.get(
            f"{JIRA_BASE_URL}/rest/api/3/search?jql={jql}&maxResults=0",
            auth=(JIRA_EMAIL, JIRA_API_TOKEN),
        )
        recent_count = tickets_response.json().get("total", 0)
        
        return {
            "department": user_data[0].get("department", "Unknown") if user_data else "Unknown",
            "recent_ticket_count": recent_count,
        }
    except Exception:
        return {"department": "Unknown", "recent_ticket_count": 0}


def check_duplicates(email, summary):
    """Check if this user has submitted a similar ticket recently."""
    jql = f'reporter = "{email}" AND created >= -7d AND status != Done'
    try:
        response = requests.get(
            f"{JIRA_BASE_URL}/rest/api/3/search?jql={jql}",
            auth=(JIRA_EMAIL, JIRA_API_TOKEN),
        )
        open_tickets = response.json().get("issues", [])
        return [
            {"key": t["key"], "summary": t["fields"]["summary"]}
            for t in open_tickets
        ]
    except Exception:
        return []
```

### Step 2: AI Classification Engine

The classification prompt is the core of the system. It takes the enriched ticket data and returns a structured classification with category, priority, and confidence score.

```python
import anthropic

client = anthropic.Anthropic(api_key=AI_API_KEY)

CLASSIFICATION_PROMPT = """You are an IT help desk triage system. Analyze the following 
support ticket and return a JSON classification.

TICKET:
- ID: {ticket_id}
- Subject: {summary}
- Description: {description}
- Submitter: {reporter_name} ({reporter_email})
- Department: {department}
- Recent tickets from this user (last 30 days): {recent_ticket_count}
- Possible duplicate open tickets: {possible_duplicates}

CLASSIFICATION RULES:
1. CATEGORY — assign exactly one:
   - PASSWORD_ACCESS: password resets, account lockouts, MFA issues, permission requests
   - NETWORK_VPN: Wi-Fi, VPN, internet connectivity, DNS issues
   - HARDWARE: laptop, monitor, keyboard, mouse, docking station, physical damage
   - SOFTWARE_INSTALL: application install/update requests, license issues, compatibility
   - EMAIL_CALENDAR: Outlook, Gmail, calendar sync, shared mailbox, distribution lists
   - PRINTER_PERIPHERAL: printer setup, scanner, webcam, headset, USB devices
   - SECURITY_INCIDENT: phishing report, suspicious activity, data breach concern, malware
   - NEW_HIRE_SETUP: onboarding, new equipment, account provisioning, first-day setup
   - OTHER: anything that doesn't fit the above categories

2. PRIORITY — assign based on impact:
   - P1_CRITICAL: affects entire company or critical business system (e.g., email server down)
   - P2_HIGH: affects an entire team or department, or a VIP/exec user is blocked
   - P3_MEDIUM: single user is blocked from doing their job
   - P4_LOW: inconvenience, how-to question, or enhancement request

3. CONFIDENCE — 0 to 100, how confident you are in the classification
4. REASONING — one sentence explaining your classification
5. IS_DUPLICATE — true/false based on the possible duplicates listed
6. SUGGESTED_TAGS — up to 3 tags for searchability

Return ONLY valid JSON:
{{
  "category": "...",
  "priority": "...",
  "confidence": 85,
  "reasoning": "...",
  "is_duplicate": false,
  "suggested_tags": ["tag1", "tag2"]
}}"""


def classify_ticket(ticket):
    """Use Claude to classify the ticket."""
    prompt = CLASSIFICATION_PROMPT.format(
        ticket_id=ticket["id"],
        summary=ticket["summary"],
        description=ticket["description"],
        reporter_name=ticket["reporter_name"],
        reporter_email=ticket["reporter_email"],
        department=ticket["user_context"]["department"],
        recent_ticket_count=ticket["user_context"]["recent_ticket_count"],
        possible_duplicates=json.dumps(ticket["possible_duplicates"]),
    )
    
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=500,
        messages=[{"role": "user", "content": prompt}],
    )
    
    result = json.loads(response.content[0].text)
    result["classified_at"] = datetime.utcnow().isoformat()
    return result
```

### Step 3: Knowledge Base Matching

This component searches your internal knowledge base using semantic similarity to find relevant resolution articles. It works with Confluence, Notion, SharePoint, or any wiki with an API.

```python
import numpy as np

def search_knowledge_base(ticket, classification):
    """Search KB for matching resolution articles using semantic search."""
    
    # Construct a search query combining the ticket content and classification
    search_query = f"{classification['category']}: {ticket['summary']}. {ticket['description'][:500]}"
    
    # Option A: Use your KB's built-in search API
    # (Confluence example)
    kb_results = requests.get(
        f"{JIRA_BASE_URL}/wiki/rest/api/content/search",
        params={"cql": f'type=page AND space=ITHELP AND text~"{ticket["summary"]}"'},
        auth=(JIRA_EMAIL, JIRA_API_TOKEN),
    ).json()
    
    # Option B: If you have embeddings set up, use semantic search
    # query_embedding = get_embedding(search_query)
    # kb_results = vector_db.similarity_search(query_embedding, top_k=5)
    
    # Also search past resolved tickets for similar issues
    jql = (
        f'project = HELP AND status = Done AND resolution = Done '
        f'AND text ~ "{ticket["summary"]}" AND resolved >= -90d '
        f'ORDER BY resolved DESC'
    )
    past_tickets = requests.get(
        f"{JIRA_BASE_URL}/rest/api/3/search?jql={jql}&maxResults=5",
        auth=(JIRA_EMAIL, JIRA_API_TOKEN),
    ).json()
    
    # Score and rank matches
    matches = []
    for article in kb_results.get("results", []):
        score = compute_relevance_score(search_query, article["title"])
        matches.append({
            "type": "kb_article",
            "title": article["title"],
            "url": f"{JIRA_BASE_URL}/wiki{article['_links']['webui']}",
            "score": score,
        })
    
    for ticket_result in past_tickets.get("issues", []):
        score = compute_relevance_score(
            search_query, ticket_result["fields"]["summary"]
        )
        matches.append({
            "type": "past_ticket",
            "title": ticket_result["fields"]["summary"],
            "key": ticket_result["key"],
            "resolution_comment": get_resolution_comment(ticket_result["key"]),
            "score": score,
        })
    
    # Sort by relevance score
    matches.sort(key=lambda x: x["score"], reverse=True)
    return matches[:3]  # Return top 3 matches


def compute_relevance_score(query, document_text):
    """Compute semantic similarity. Replace with your preferred method."""
    # Simple keyword overlap for demonstration
    # In production, use embeddings (OpenAI, Cohere, or local model)
    query_words = set(query.lower().split())
    doc_words = set(document_text.lower().split())
    if not query_words:
        return 0.0
    overlap = len(query_words & doc_words)
    return round((overlap / len(query_words)) * 100, 1)


def get_resolution_comment(ticket_key):
    """Get the resolution comment from a past ticket."""
    try:
        response = requests.get(
            f"{JIRA_BASE_URL}/rest/api/3/issue/{ticket_key}/comment",
            auth=(JIRA_EMAIL, JIRA_API_TOKEN),
        )
        comments = response.json().get("comments", [])
        if comments:
            return comments[-1]["body"]
        return "No resolution comment found."
    except Exception:
        return "Unable to retrieve resolution."
```

### Step 4: Auto-Response Drafting & Escalation Routing

```python
RESPONSE_PROMPT = """You are a friendly, professional IT help desk technician. Draft a 
response to the following support ticket using the matched knowledge base article.

TICKET:
- Subject: {summary}
- From: {reporter_name}
- Description: {description}

MATCHED RESOLUTION (confidence: {match_score}%):
{resolution_content}

GUIDELINES:
- Address the user by first name
- Acknowledge their issue in one sentence
- Provide clear, numbered step-by-step instructions
- Include screenshots links from the KB article if referenced
- End with "If this doesn't resolve your issue, reply to this ticket and we'll 
  investigate further."
- Keep the tone warm but efficient
- If the resolution involves a password reset or security action, remind them 
  never to share credentials via email

Write the response as if it's coming from the help desk team (sign off as 
"IT Help Desk Team")."""


def draft_response(ticket, kb_matches, classification):
    """Draft an auto-response if we have a high-confidence KB match."""
    
    if not kb_matches or kb_matches[0]["score"] < 70:
        return None  # No confident match, skip auto-response
    
    best_match = kb_matches[0]
    
    prompt = RESPONSE_PROMPT.format(
        summary=ticket["summary"],
        reporter_name=ticket["reporter_name"].split()[0],
        description=ticket["description"][:1000],
        match_score=best_match["score"],
        resolution_content=best_match.get("resolution_comment", best_match["title"]),
    )
    
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=800,
        messages=[{"role": "user", "content": prompt}],
    )
    
    return {
        "draft_text": response.content[0].text,
        "kb_match": best_match,
        "confidence": best_match["score"],
        "status": "auto_draft" if best_match["score"] >= 90 else "suggested",
    }


def route_ticket(ticket, classification):
    """Route the ticket based on classification results."""
    
    # Step 1: Update ticket fields in Jira
    update_payload = {
        "fields": {
            "priority": {"name": priority_map(classification["priority"])},
            "labels": classification["suggested_tags"],
        }
    }
    requests.put(
        f"{JIRA_BASE_URL}/rest/api/3/issue/{ticket['id']}",
        json=update_payload,
        auth=(JIRA_EMAIL, JIRA_API_TOKEN),
    )
    
    # Step 2: Handle by priority and category
    if classification["priority"] == "P1_CRITICAL":
        # Page the on-call engineer immediately
        send_pagerduty_alert(ticket, classification)
        send_slack_alert("#it-critical", ticket, classification)
        
    elif classification["category"] == "SECURITY_INCIDENT":
        # Route to security team regardless of priority
        assign_to_group(ticket["id"], "security-team")
        send_slack_alert("#security-incidents", ticket, classification)
        
    elif classification["confidence"] < 75:
        # Low confidence — flag for senior technician
        assign_to_group(ticket["id"], "tier2-review")
        add_internal_comment(
            ticket["id"],
            f"[AUTO-TRIAGE] Low confidence ({classification['confidence']}%). "
            f"Suggested category: {classification['category']}. "
            f"Reason: {classification['reasoning']}"
        )
        
    else:
        # Standard ticket — search KB and draft response
        kb_matches = search_knowledge_base(ticket, classification)
        draft = draft_response(ticket, kb_matches, classification)
        
        if draft and draft["confidence"] >= 90:
            # High-confidence auto-response — add as draft internal note
            add_internal_comment(
                ticket["id"],
                f"[AUTO-DRAFT] Ready to send (confidence: {draft['confidence']}%):\n\n"
                f"{draft['draft_text']}\n\n"
                f"KB Source: {draft['kb_match']['title']}\n"
                f"→ Click 'Approve' to send to user, or edit as needed."
            )
        elif draft:
            # Medium-confidence — suggest but flag for review
            add_internal_comment(
                ticket["id"],
                f"[SUGGESTED] Possible match (confidence: {draft['confidence']}%):\n\n"
                f"{draft['draft_text']}\n\n"
                f"⚠️ Review before sending — match confidence is below auto-send threshold."
            )


def priority_map(ai_priority):
    """Map AI priority to Jira priority names."""
    return {
        "P1_CRITICAL": "Highest",
        "P2_HIGH": "High",
        "P3_MEDIUM": "Medium",
        "P4_LOW": "Low",
    }.get(ai_priority, "Medium")


def send_slack_alert(channel, ticket, classification):
    """Send a Slack alert for critical/security tickets."""
    # Implement with Slack webhook or API
    pass


def send_pagerduty_alert(ticket, classification):
    """Page the on-call engineer for P1 critical issues."""
    # Implement with PagerDuty Events API
    pass


def assign_to_group(ticket_id, group_name):
    """Assign ticket to a specific team/group."""
    # Implement with your ticketing system's API
    pass


def add_internal_comment(ticket_id, comment_text):
    """Add an internal comment to the ticket."""
    requests.post(
        f"{JIRA_BASE_URL}/rest/api/3/issue/{ticket_id}/comment",
        json={
            "body": {"type": "doc", "version": 1, "content": [
                {"type": "paragraph", "content": [
                    {"type": "text", "text": comment_text}
                ]}
            ]},
            "properties": [{"key": "sd.public.comment", "value": {"internal": True}}]
        },
        auth=(JIRA_EMAIL, JIRA_API_TOKEN),
    )
```

### Step 5: Daily Metrics Report Generator

This script runs at end-of-day (via cron job or scheduled task) and compiles a comprehensive metrics report.

```python
from datetime import datetime, timedelta
import json

def generate_daily_report():
    """Generate end-of-day help desk metrics report."""
    
    today = datetime.now().strftime("%Y-%m-%d")
    
    # Fetch today's ticket data
    jql_created = f'project = HELP AND created >= "{today}" AND created < "{today}+1d"'
    jql_resolved = f'project = HELP AND resolved >= "{today}" AND resolved < "{today}+1d"'
    jql_open = f'project = HELP AND status NOT IN (Done, Closed)'
    
    created_tickets = fetch_tickets(jql_created)
    resolved_tickets = fetch_tickets(jql_resolved)
    open_backlog = fetch_tickets(jql_open)
    
    # Calculate metrics
    metrics = {
        "date": today,
        "tickets_created": len(created_tickets),
        "tickets_resolved": len(resolved_tickets),
        "open_backlog": len(open_backlog),
        "resolution_rate": round(
            len(resolved_tickets) / max(len(created_tickets), 1) * 100, 1
        ),
    }
    
    # Category breakdown
    categories = {}
    for ticket in created_tickets:
        cat = ticket.get("category", "Uncategorized")
        categories[cat] = categories.get(cat, 0) + 1
    metrics["category_breakdown"] = dict(
        sorted(categories.items(), key=lambda x: x[1], reverse=True)
    )
    
    # Priority breakdown
    priorities = {"P1": 0, "P2": 0, "P3": 0, "P4": 0}
    for ticket in created_tickets:
        p = ticket.get("priority", "P3")
        if p in priorities:
            priorities[p] += 1
    metrics["priority_breakdown"] = priorities
    
    # Average response time (time from creation to first comment)
    response_times = []
    for ticket in resolved_tickets:
        if ticket.get("first_response_time"):
            response_times.append(ticket["first_response_time"])
    metrics["avg_response_time_minutes"] = (
        round(sum(response_times) / len(response_times), 1) if response_times else None
    )
    
    # Auto-resolution stats
    auto_resolved = [t for t in resolved_tickets if t.get("auto_resolved")]
    metrics["auto_resolved_count"] = len(auto_resolved)
    metrics["auto_resolved_pct"] = round(
        len(auto_resolved) / max(len(resolved_tickets), 1) * 100, 1
    )
    
    # SLA compliance
    sla_met = [t for t in resolved_tickets if t.get("sla_met", True)]
    metrics["sla_compliance_pct"] = round(
        len(sla_met) / max(len(resolved_tickets), 1) * 100, 1
    )
    
    # Repeat submitters (users with 5+ tickets this month)
    repeat_users = identify_repeat_submitters()
    metrics["repeat_submitters"] = repeat_users
    
    # Emerging patterns — use AI to spot trends
    trend_analysis = analyze_trends(created_tickets)
    metrics["trend_analysis"] = trend_analysis
    
    # Generate the report
    report = format_report(metrics)
    
    # Send via Slack and email
    send_slack_report("#it-helpdesk", report)
    send_email_report("it-lead@yourcompany.com", report, metrics)
    
    return metrics


TREND_ANALYSIS_PROMPT = """Analyze the following IT help desk tickets from today and 
identify patterns, emerging issues, or trends that the team lead should be aware of.

TICKETS OPENED TODAY:
{tickets_json}

Provide:
1. Top 3 issue patterns (what's happening most and why)
2. Any emerging issues (new types of problems that haven't appeared before)
3. Recommendations (proactive steps to reduce ticket volume)

Keep it concise — 3-4 sentences per section. This goes into a daily report for the 
IT team lead."""


def analyze_trends(tickets):
    """Use AI to identify trends in today's tickets."""
    tickets_summary = [
        {"summary": t["summary"], "category": t.get("category", "Unknown")}
        for t in tickets[:30]  # Cap at 30 for prompt size
    ]
    
    prompt = TREND_ANALYSIS_PROMPT.format(tickets_json=json.dumps(tickets_summary, indent=2))
    
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        max_tokens=600,
        messages=[{"role": "user", "content": prompt}],
    )
    
    return response.content[0].text


def format_report(metrics):
    """Format metrics into a readable daily report."""
    report = f"""
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  IT HELP DESK DAILY REPORT — {metrics['date']}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 KEY METRICS
  Tickets Opened:     {metrics['tickets_created']}
  Tickets Resolved:   {metrics['tickets_resolved']}
  Open Backlog:       {metrics['open_backlog']}
  Resolution Rate:    {metrics['resolution_rate']}%
  Avg Response Time:  {metrics['avg_response_time_minutes'] or 'N/A'} min
  SLA Compliance:     {metrics['sla_compliance_pct']}%

🤖 AUTOMATION IMPACT
  Auto-Resolved:      {metrics['auto_resolved_count']} ({metrics['auto_resolved_pct']}%)
  Est. Time Saved:    {metrics['auto_resolved_count'] * 4} min 
                      ({round(metrics['auto_resolved_count'] * 4 / 60, 1)} hrs)

📂 CATEGORY BREAKDOWN"""
    
    for cat, count in metrics["category_breakdown"].items():
        bar = "█" * min(count, 20)
        report += f"\n  {cat:<22} {bar} {count}"
    
    report += f"""

🔺 PRIORITY DISTRIBUTION
  P1 Critical:  {metrics['priority_breakdown']['P1']}
  P2 High:      {metrics['priority_breakdown']['P2']}
  P3 Medium:    {metrics['priority_breakdown']['P3']}
  P4 Low:       {metrics['priority_breakdown']['P4']}

📈 TREND ANALYSIS
{metrics.get('trend_analysis', 'No trend analysis available.')}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
"""
    return report
```

---

## Example Execution

### Scenario: Monday morning ticket surge

A company of 500 employees returns from the weekend. By 9:30 AM, 23 tickets have been submitted. Here's how the automated workflow handles three of them:

**Ticket #1: "Can't connect to VPN from home"**

| Step | Manual Process | Automated Process |
|------|---------------|-------------------|
| Read & classify | Tech reads ticket, decides it's VPN/Network, Priority Medium (3 min) | AI classifies in 2 seconds: `NETWORK_VPN`, `P3_MEDIUM`, confidence 94% |
| Search KB | Tech searches "VPN troubleshooting" in Confluence, finds article (2 min) | Semantic search returns "VPN Connection Troubleshooting Guide" — 96% match |
| Draft response | Tech types out 8-step VPN fix instructions (4 min) | Auto-draft generated with personalized steps for user's OS (Windows 11), queued for review |
| Send | Tech reviews, sends (1 min) | Tech clicks "Approve" — response sent (10 sec) |
| **Total** | **10 minutes** | **15 seconds of human time** |

**Ticket #2: "URGENT — Salesforce is down for our entire team"**

| Step | Manual Process | Automated Process |
|------|---------------|-------------------|
| Read & classify | Tech reads, escalates to senior (5 min including context-writing) | AI classifies: `SOFTWARE_INSTALL`, `P1_CRITICAL`, confidence 91%. Auto-escalation triggered. |
| Alert team | Tech Slacks the on-call engineer, explains situation (3 min) | PagerDuty alert + Slack message with full context sent in 3 seconds |
| **Total** | **8 minutes** | **3 seconds** |

**Ticket #3: "How do I add a shared mailbox in Outlook?"**

| Step | Manual Process | Automated Process |
|------|---------------|-------------------|
| Read & classify | Tech reads, recognizes as a how-to (1 min) | AI classifies: `EMAIL_CALENDAR`, `P4_LOW`, confidence 97% |
| Find instructions | Tech remembers KB article, pulls it up (2 min) | KB match: "Add Shared Mailbox — Outlook Desktop & Web" — 98% match |
| Draft response | Tech copies KB steps, personalizes greeting (3 min) | Auto-draft with platform-specific steps, self-service link included |
| **Total** | **6 minutes** | **10 seconds of human time** |

**Monday morning result:** Instead of spending 2.5 hours processing 23 tickets manually, the technician reviews and approves auto-drafted responses in about 15 minutes, focusing their attention on the 6–8 tickets that actually require investigation.

---

## Visual Example: Daily Report Dashboard

Here's what the daily metrics dashboard looks like when rendered as an HTML report or embedded in Slack:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  IT HELP DESK DAILY REPORT — 2026-04-22
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 KEY METRICS
  Tickets Opened:     47
  Tickets Resolved:   43
  Open Backlog:       12
  Resolution Rate:    91.5%
  Avg Response Time:  8.3 min
  SLA Compliance:     96.2%

🤖 AUTOMATION IMPACT
  Auto-Resolved:      29 (67.4%)
  Est. Time Saved:    116 min (1.9 hrs)

📂 CATEGORY BREAKDOWN
  PASSWORD_ACCESS        ████████████ 12
  NETWORK_VPN            ████████ 8
  SOFTWARE_INSTALL       ███████ 7
  EMAIL_CALENDAR         ██████ 6
  PRINTER_PERIPHERAL     █████ 5
  HARDWARE               ████ 4
  NEW_HIRE_SETUP         ███ 3
  SECURITY_INCIDENT      ██ 2

🔺 PRIORITY DISTRIBUTION
  P1 Critical:  1
  P2 High:      4
  P3 Medium:    28
  P4 Low:       14

📈 TREND ANALYSIS
  Top patterns: Password resets spiked (12 tickets) — likely related 
  to the forced password rotation policy enacted Friday. VPN issues 
  clustered among remote employees using macOS Sequoia 15.4.

  Emerging: 3 tickets about "Salesforce loading slowly" from different 
  departments — may indicate a platform-side performance issue. 
  Recommend checking Salesforce status page and notifying affected teams.

  Recommendation: Publish a self-service password reset guide via 
  company-wide Slack announcement to reduce tomorrow's volume. 
  Consider adding the macOS VPN fix to the employee onboarding checklist.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Why This Should Be Implemented

**The math is simple.** A help desk technician handling 50 tickets/day at an average of 3 minutes per ticket spends 2.5 hours on triage and response. This workflow automates 65–70% of those tickets, saving roughly 1.5–2 hours daily — that's **10+ hours per week** returned to proactive work like documentation, training, and infrastructure improvements.

**The compounding benefits go beyond time savings:**

1. **Faster response times** — Users get acknowledged in seconds instead of waiting in a queue. For P1 critical issues, the difference between a 30-second auto-escalation and a 15-minute manual read-and-escalate can prevent company-wide productivity loss.

2. **Consistent quality** — Every response follows the same KB-backed, step-by-step format. No more variance between a senior tech's thorough response and a junior tech's hasty one-liner.

3. **Knowledge base feedback loop** — The system identifies which KB articles are most frequently matched (keep them updated) and which ticket types have *no* KB match (write new articles). Over time, the auto-resolution rate climbs from 65% toward 80%+.

4. **Proactive issue detection** — The trend analysis catches patterns that a busy technician would miss: a sudden spike in VPN tickets after a network change, or three unrelated users all reporting the same SaaS tool being slow. These early signals enable the team to address root causes before the queue explodes.

5. **Technician satisfaction** — Help desk burnout is real. Removing the repetitive read-classify-search-draft loop lets technicians focus on the interesting, complex problems that actually use their skills — the 30% of tickets that require genuine troubleshooting.

**Implementation cost is low.** The workflow runs on a Python webhook handler, an AI API, and your existing ticketing system's API. No new infrastructure required. A competent IT team can have a proof-of-concept running in 2–3 days using the code templates above, starting with a single ticket category (e.g., password resets) before expanding to the full taxonomy.

---

## Getting Started Checklist

- [ ] Enable webhooks in your ticketing system (Jira/Zendesk/Freshdesk)
- [ ] Set up a lightweight Python server (Flask/FastAPI) to receive webhooks
- [ ] Get an API key from Anthropic (Claude) or OpenAI for the classification engine
- [ ] Export your top 20 KB articles into a searchable format
- [ ] Start with one category (PASSWORD_ACCESS) — verify accuracy over 50 tickets
- [ ] Expand to all categories once accuracy exceeds 90%
- [ ] Set up the daily report cron job
- [ ] Train technicians on the "review and approve" workflow
- [ ] Monitor auto-resolution rate weekly and update KB articles accordingly

---

*Blueprint created: April 22, 2026*  
*Category: IT Operations / Technical Support*  
*Estimated implementation time: 2–3 days for MVP, 1–2 weeks for full deployment*
