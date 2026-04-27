# Blueprint: Marketing Manager — Weekly Multi-Channel Campaign Performance & Budget Reallocation Report

**Role:** Marketing Manager / Paid Media Manager / Growth Marketer
**Industry:** SaaS, E-commerce, DTC, B2B Services, Agencies
**Task Automated:** Weekly pulling, normalizing, and synthesizing paid-media data across Google Ads, Meta (Facebook/Instagram) Ads, LinkedIn Ads, TikTok Ads, GA4, and the CRM, then producing a leadership-ready performance report with channel-level ROAS, attribution, anomalies, and budget reallocation recommendations.
**Time Saved:** ~9 hours/week (every Monday morning + mid-week check-ins)
**Output:** A polished, single-page executive PDF + shareable HTML dashboard + Slack/email summary, delivered automatically every Monday at 7:00 AM.

---

## The Problem

Marketing Managers at small-to-midsize companies (Seed to Series C, or agencies managing 5–20 clients) spend the first full day of every week trapped in spreadsheet gymnastics:

1. Log into **Google Ads** → export Campaign, Ad Group, and Keyword reports to CSV.
2. Log into **Meta Ads Manager** → filter by last 7 days, export Campaign + Ad Set performance.
3. Log into **LinkedIn Campaign Manager** → pull Sponsored Content and Message Ads metrics.
4. Log into **TikTok Ads Manager** → export campaign-level spend and conversion data.
5. Log into **GA4** → build or refresh Explorations for landing page conversion, traffic-source revenue, and e-commerce/funnel events.
6. Log into **HubSpot / Salesforce** → pull SQLs, opportunities, and closed-won deals by UTM source/medium.
7. Dump all of it into a master Google Sheet → manually reconcile currency, attribution windows, and UTM taxonomy.
8. Build a slide or one-pager for the Monday leadership meeting: "What's working, what's not, what should we shift budget to?"

This weekly ritual consumes **7–10 hours**, is riddled with copy-paste errors, and — worst of all — by the time leadership sees it, the data is already 24–72 hours stale. Budget decisions are made on gut feel because there's no time left for actual analysis. Channels bleed money for weeks before anyone notices underperformance, and winning campaigns don't get scaled fast enough.

---

## The Automated Workflow

### Architecture Overview

```
┌────────────────────────────────────────────────────────────────────┐
│                    SUNDAY NIGHT DATA COLLECTION                     │
│                      (Scheduled: Sunday 11:00 PM)                   │
├─────────────┬──────────────┬──────────────┬──────────────┬─────────┤
│ Google Ads  │  Meta Ads    │  LinkedIn    │  TikTok Ads  │  GA4    │
│  API        │  Graph API   │  Ads API     │  Business API│  Data   │
│             │              │              │              │  API    │
└──────┬──────┴──────┬───────┴──────┬───────┴──────┬───────┴────┬────┘
       │             │              │              │            │
       └─────────────┴──────┬───────┴──────────────┴────────────┘
                            │
                            ▼
       ┌─────────────────────────────────────┐
       │   CRM CONVERSION PULL (HubSpot/SF)  │
       │   Matches UTMs → Deals → Revenue    │
       └─────────────────┬───────────────────┘
                         │
                         ▼
┌───────────────────────────────────────────────────────────────┐
│              UNIFIED MARKETING DATA WAREHOUSE                  │
│         (DuckDB or BigQuery — normalized UTM schema)          │
└─────────────────────────────┬─────────────────────────────────┘
                              │
                              ▼
┌───────────────────────────────────────────────────────────────┐
│                   AI ANALYSIS ENGINE                           │
│                                                                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐ │
│  │ ROAS / CAC   │  │ Week-over-   │  │ Anomaly Detection    │ │
│  │ Calculator   │  │ Week Trend   │  │ (CPC, CVR, CPA drift)│ │
│  └──────┬───────┘  └──────┬───────┘  └──────────┬───────────┘ │
│         │                 │                      │             │
│         ▼                 ▼                      ▼             │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │   Budget Reallocation Optimizer (LP + heuristic rules)   │ │
│  │   Input: channel performance + remaining monthly budget  │ │
│  │   Output: $X to shift from Channel A → Channel B/Campaign│ │
│  └──────────────────────────┬───────────────────────────────┘ │
│                             ▼                                  │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │   LLM Narrative Writer (Claude API)                      │ │
│  │   Turns numbers into exec-ready insights + action list   │ │
│  └──────────────────────────┬───────────────────────────────┘ │
└──────────────────────────────┼────────────────────────────────┘
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                     REPORT GENERATION                         │
│                                                               │
│  ┌──────────────┐  ┌───────────────┐  ┌──────────────────┐   │
│  │ Exec PDF     │  │ HTML Dashboard│  │ Slack Digest     │   │
│  │ (1 page)     │  │ (shareable)   │  │ #marketing-wkly  │   │
│  └──────────────┘  └───────────────┘  └──────────────────┘   │
│                                                               │
│         Delivered: Monday 7:00 AM via email + Slack           │
└──────────────────────────────────────────────────────────────┘
```

### Step-by-Step Workflow

**Step 1 — Sunday Night Data Collection (11:00 PM, Automated)**

A scheduled Python job pulls the previous 7-day performance window (Mon–Sun) from every ad platform:

- **Google Ads API** → Campaign + Ad Group level: impressions, clicks, cost, conversions, conversion value
- **Meta Marketing API** → Campaign + Ad Set level: spend, CTR, CPM, purchases, purchase ROAS, CPA
- **LinkedIn Marketing API** → Campaign level: impressions, clicks, leads, form completions, cost
- **TikTok Ads API** → Campaign level: spend, CPC, CPM, conversions
- **GA4 Data API** → Session source/medium/campaign, landing-page conversion rate, engaged sessions, revenue

**Step 2 — CRM Revenue Attribution Pull (11:20 PM, Automated)**

Queries HubSpot (or Salesforce) for deals **created** and **closed-won** in the last 7 days, joined against the `hs_analytics_source`, `hs_analytics_first_url`, and UTM parameters on the originating contact/deal. This is the ground-truth revenue attribution layer — platform pixels lie, CRM doesn't.

**Step 3 — Data Normalization (11:30 PM, Automated)**

All rows are normalized to a shared `unified_campaigns` schema:

```python
# campaigns_schema.py
import pandas as pd
from dataclasses import dataclass
from datetime import date

@dataclass
class CampaignRow:
    date: date
    channel: str            # 'google', 'meta', 'linkedin', 'tiktok', 'organic'
    campaign_id: str
    campaign_name: str
    utm_source: str
    utm_medium: str
    utm_campaign: str
    impressions: int
    clicks: int
    spend_usd: float
    conversions: int         # platform-reported
    crm_leads: int           # from HubSpot/Salesforce
    crm_sqls: int
    crm_opps: int
    crm_closed_won: int
    crm_revenue_usd: float

def normalize_all(raw_by_channel: dict) -> pd.DataFrame:
    """Converts each platform's native schema into unified_campaigns."""
    rows = []
    rows += _normalize_google(raw_by_channel['google_ads'])
    rows += _normalize_meta(raw_by_channel['meta_ads'])
    rows += _normalize_linkedin(raw_by_channel['linkedin_ads'])
    rows += _normalize_tiktok(raw_by_channel['tiktok_ads'])
    df = pd.DataFrame([vars(r) for r in rows])

    # FX convert any non-USD to USD via ECB rates for the period
    df['spend_usd'] = df.apply(_fx_to_usd, axis=1)

    # Join CRM attribution on utm_source + utm_medium + utm_campaign
    df = df.merge(_load_crm_attribution(), on=['utm_source','utm_medium','utm_campaign'], how='left')

    # Fill nulls, compute derived metrics
    df['ctr']       = df['clicks'] / df['impressions'].replace(0, pd.NA)
    df['cpc']       = df['spend_usd'] / df['clicks'].replace(0, pd.NA)
    df['cpa_crm']   = df['spend_usd'] / df['crm_sqls'].replace(0, pd.NA)
    df['roas_crm']  = df['crm_revenue_usd'] / df['spend_usd'].replace(0, pd.NA)
    return df
```

**Step 4 — Anomaly Detection (11:45 PM, Automated)**

Compares this week's metrics against a rolling 4-week baseline per campaign. Any metric that drifts beyond ±2 standard deviations gets flagged:

```python
def detect_anomalies(df_this_week: pd.DataFrame, df_baseline: pd.DataFrame):
    """Flags campaigns with significant metric drift."""
    anomalies = []
    metrics = ['cpc', 'ctr', 'cpa_crm', 'roas_crm']

    for campaign_id, this in df_this_week.groupby('campaign_id'):
        baseline = df_baseline[df_baseline['campaign_id'] == campaign_id]
        if len(baseline) < 14:  # need min 2 weeks of history
            continue
        for m in metrics:
            mean, std = baseline[m].mean(), baseline[m].std()
            if std == 0 or pd.isna(std):
                continue
            z = (this[m].iloc[0] - mean) / std
            if abs(z) >= 2.0:
                anomalies.append({
                    'campaign': this['campaign_name'].iloc[0],
                    'channel':  this['channel'].iloc[0],
                    'metric':   m,
                    'this_week': round(this[m].iloc[0], 2),
                    'baseline': round(mean, 2),
                    'z_score':  round(z, 2),
                    'direction': 'deteriorated' if (m in ['cpc','cpa_crm'] and z > 0) or (m in ['ctr','roas_crm'] and z < 0) else 'improved'
                })
    return anomalies
```

**Step 5 — Budget Reallocation Optimizer (11:55 PM, Automated)**

A simple linear-programming model (via `scipy.optimize`) that, given the remaining monthly budget, suggests how to reallocate dollars toward higher-ROAS campaigns, subject to per-channel min/max constraints:

```python
from scipy.optimize import linprog

def optimize_budget_shift(campaigns_df, remaining_budget, constraints):
    """
    Suggests how to redistribute `remaining_budget` across campaigns
    to maximize expected incremental CRM revenue (ROAS-weighted),
    subject to per-channel floors/caps set in `constraints`.
    Returns a list of {campaign, current_weekly_spend, suggested_weekly_spend, delta}.
    """
    # Maximize sum(roas_i * x_i)  →  minimize -roas
    c = -campaigns_df['roas_crm'].fillna(0).values

    # Constraint: sum of x_i == remaining_budget
    A_eq = [[1] * len(campaigns_df)]
    b_eq = [remaining_budget]

    # Bounds: per-campaign min/max derived from `constraints`
    bounds = [
        (constraints[row.channel]['min_per_campaign'],
         constraints[row.channel]['max_per_campaign'])
        for _, row in campaigns_df.iterrows()
    ]
    res = linprog(c, A_eq=A_eq, b_eq=b_eq, bounds=bounds, method='highs')
    return _format_recommendations(campaigns_df, res.x)
```

**Step 6 — LLM Narrative Generation (12:05 AM, Automated)**

Claude API turns the numbers into plain-English insights an executive actually reads. The system prompt:

```
You are the CMO's Chief of Staff. Given the attached JSON of weekly paid-media
performance, anomalies, and budget recommendations, write a one-page weekly
briefing in this exact structure:

1. HEADLINE (one sentence — the single most important thing leadership should know)
2. WINS (3 bullets — campaigns/channels that materially outperformed)
3. CONCERNS (3 bullets — campaigns/channels that are bleeding money or declining)
4. RECOMMENDED ACTIONS (numbered list of 3–5 specific budget shifts with exact
   dollar amounts and the expected lift, referencing the optimizer output)
5. WATCH LIST (1–3 things to re-check next week)

Rules:
- Lead with business outcomes (revenue, CAC, ROAS), not vanity metrics
  (impressions, CTR) unless CTR anomalies are the signal.
- Every claim must cite a number from the data.
- Do not use marketing jargon. The CEO reads this.
- Keep the whole thing under 350 words.
```

**Step 7 — Report Delivery (Monday 7:00 AM, Automated)**

- **Executive PDF** (one page, branded) → emailed to the CMO + CEO
- **HTML Dashboard** → hosted on internal S3/Vercel, linked in Slack, with drill-downs per channel/campaign
- **Slack digest** → posted in `#marketing-weekly` with key numbers + link to full report

---

## Example Execution

### Trigger (Cron)

```bash
# /etc/cron.d/marketing-weekly-report
0 23 * * 0  marketing  /usr/bin/python3 /opt/mktg-report/run_weekly.py --week last --deliver all
```

### Sample Terminal Run

```
$ python run_weekly.py --week last --deliver all
[2026-04-12 23:00:01] Pulling Google Ads (12 campaigns)........... OK  $14,238 spend
[2026-04-12 23:00:18] Pulling Meta Ads (9 campaigns).............. OK   $8,902 spend
[2026-04-12 23:00:31] Pulling LinkedIn Ads (4 campaigns).......... OK   $3,610 spend
[2026-04-12 23:00:42] Pulling TikTok Ads (3 campaigns)............ OK   $2,105 spend
[2026-04-12 23:00:55] Pulling GA4 events (landing page + revenue). OK   48,912 sessions
[2026-04-12 23:01:14] Pulling HubSpot deals (utm-matched)......... OK   142 SQLs / 18 closed-won / $231,400 revenue
[2026-04-12 23:01:38] Normalizing → unified_campaigns.............. OK   28 campaigns × 7 days = 196 rows
[2026-04-12 23:01:42] Running anomaly detection (z≥2.0)........... OK   4 anomalies flagged
[2026-04-12 23:01:46] Running budget optimizer (LP, $38k remaining) OK   5 reallocations suggested
[2026-04-12 23:02:09] Claude narrative generation................. OK   312 words
[2026-04-12 23:02:15] Rendering exec PDF (WeasyPrint).............. OK   weekly_2026-W15.pdf
[2026-04-12 23:02:18] Publishing HTML dashboard...................  OK   https://mktg.acme.com/w/2026-15
[2026-04-12 23:02:21] Posting Slack digest to #marketing-weekly....  OK
[2026-04-12 23:02:22] Sending email to cmo@acme.com, ceo@acme.com.  OK

Weekly report delivered in 2m 21s. Next run: Sun 2026-04-19 23:00.
```

---

## Visual Example of the Delivered Artifact

```
╔══════════════════════════════════════════════════════════════════════════╗
║   ACME MARKETING — WEEKLY PERFORMANCE BRIEF   |   Week 15 (Apr 6–12)     ║
╠══════════════════════════════════════════════════════════════════════════╣
║                                                                          ║
║  HEADLINE                                                                ║
║  ──────────────────────────────────────────────────────────────────      ║
║  Blended ROAS fell to 2.9x (↓18% WoW) driven entirely by Meta            ║
║  Prospecting; reallocating $6.2k to Google Brand + LinkedIn ABM this     ║
║  week projects to recover ~$41k in pipeline.                             ║
║                                                                          ║
║  ─────── KEY NUMBERS ───────────────────────────────────────────────     ║
║   Spend          $28,855       ▼  4%                                     ║
║   CRM Revenue    $231,400      ▼ 22%                                     ║
║   Blended ROAS   2.90x         ▼ 18%   (target: 3.5x)                    ║
║   SQLs           142           ▼  9%                                     ║
║   Blended CAC    $202          ▲ 14%                                     ║
║                                                                          ║
║  ─────── CHANNEL SCORECARD ─────────────────────────────────────────     ║
║   Channel     Spend     ROAS    vs 4wk    SQLs    Verdict                ║
║   ────────────────────────────────────────────────────────────           ║
║   Google      $14.2k    4.1x    ▲  6%     71      ✅ Scale               ║
║   Meta         $8.9k    1.4x    ▼ 44%     32      ⚠️  Cut / Refresh      ║
║   LinkedIn     $3.6k    5.8x    ▲ 12%     24      ✅ Scale ABM           ║
║   TikTok       $2.1k    2.2x    —         15      ⏸  Hold, test creative ║
║                                                                          ║
║  ─────── WINS ──────────────────────────────────────────────────────     ║
║   • Google "Brand-Exact" campaign: ROAS 11.2x, CPA $38 (best ever)       ║
║   • LinkedIn ABM Tier-1 accounts: 12 SQLs at $153 CPA (↓31% WoW)         ║
║   • GA4 /pricing page conversion: 4.8% (↑ from 3.6% after H1 rewrite)    ║
║                                                                          ║
║  ─────── CONCERNS ──────────────────────────────────────────────────     ║
║   • Meta "Cold Prospecting — US" CPA ballooned $58→$139 (creative        ║
║     fatigue, frequency 6.2)                                              ║
║   • TikTok "Feature Launch" CTR dropped 1.4%→0.6% (hook not landing)     ║
║   • LinkedIn InMail campaign: 0 replies on 220 sends — pause             ║
║                                                                          ║
║  ─────── RECOMMENDED ACTIONS THIS WEEK ─────────────────────────────     ║
║   1. Shift $4,500/wk from Meta Cold Prospecting → Google Brand-Exact     ║
║      (projected +$18k pipeline @ historical ROAS)                        ║
║   2. Shift $1,700/wk from TikTok Feature Launch → LinkedIn ABM Tier-1    ║
║      (projected +$9.8k pipeline)                                         ║
║   3. Pause LinkedIn InMail campaign entirely ($600 saved)                ║
║   4. Refresh 3 Meta ad creatives before re-enabling prospecting          ║
║   5. Set Google "Brand-Exact" daily cap +25% (currently budget-capped)   ║
║                                                                          ║
║  ─────── WATCH LIST NEXT WEEK ──────────────────────────────────────     ║
║   • Meta creative refresh performance (days 1–3)                         ║
║   • Does Google Brand incremental spend hold ROAS >8x?                   ║
║   • Q2 campaign launch readiness                                         ║
║                                                                          ║
║  Full dashboard: https://mktg.acme.com/w/2026-15                         ║
╚══════════════════════════════════════════════════════════════════════════╝
```

The HTML dashboard adds interactive charts: spend-vs-revenue by channel, funnel drop-off per landing page, SQL → closed-won velocity by campaign, and a reallocation slider that lets the CMO "what-if" the recommendations.

---

## Prompts

### 1. Narrative Generation Prompt (Claude API)

```
SYSTEM:
You are the Chief of Staff to the CMO of a growth-stage company. You produce
weekly paid-media briefings that executives actually read — short, number-led,
decision-oriented.

USER:
Generate this week's marketing brief from the attached JSON.

Data contains:
- weekly_summary:  spend, crm_revenue, blended_roas, sqls, cac (this week vs 4-wk avg)
- channel_scorecard: per-channel roas, spend, sqls, wow deltas
- anomalies: list of campaigns with z-score flagged metric drift
- optimizer_recommendations: list of budget shift suggestions with projected pipeline impact
- historical_context: last 4 weeks of blended metrics

Output format (Markdown, ≤ 350 words):
## Headline
<one sentence>

## Wins
- <bullet with number>
- <bullet with number>
- <bullet with number>

## Concerns
- <bullet with number>
- <bullet with number>
- <bullet with number>

## Recommended Actions
1. <action with $ amount + projected lift>
...

## Watch List Next Week
- <item>
- <item>

Rules:
- Cite a number in every bullet.
- Reference campaign names exactly as provided.
- If an anomaly contradicts a recommendation, surface the tension.
- Do not hedge. The CMO wants recommendations, not options.
```

### 2. UTM Reconciliation Prompt (used when CRM UTM data is messy)

```
You will receive two lists:
1. Canonical UTM taxonomy (source, medium, campaign patterns we expect)
2. Actual UTMs found in the CRM this week

For each non-matching UTM, propose the most likely canonical mapping based on
fuzzy string similarity and domain knowledge of digital marketing channels.

Return JSON: [{ actual_utm: {...}, suggested_canonical: {...}, confidence: 0.0–1.0, reasoning: "..." }]

Flag confidence < 0.7 for human review.
```

---

## Setup Guide

### Prerequisites

| Item | Notes |
|---|---|
| Python 3.11+ | Runtime |
| Google Ads API developer token | Requires Google Ads Manager account + basic approval |
| Meta Marketing API app | Meta Business verification required |
| LinkedIn Marketing API app | LinkedIn Developer + Marketing Developer Platform access |
| TikTok Ads API app | Via TikTok for Business |
| GA4 Data API service account | IAM "Viewer" on the GA4 property |
| HubSpot Private App token *or* Salesforce Connected App | Read scope for Deals, Contacts, Companies |
| Anthropic API key | For Claude narrative generation |
| Hosting | Any VM or serverless scheduler (Render, Railway, AWS Lambda + EventBridge, GitHub Actions on cron) |
| Storage | DuckDB file (simple) *or* BigQuery / Postgres (scale) |
| PDF rendering | WeasyPrint or Playwright |

### Install

```bash
git clone https://github.com/heymarii/marketing-weekly-blueprint
cd marketing-weekly-blueprint
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env   # fill in your API keys
python scripts/init_db.py
python run_weekly.py --week last --deliver dry-run   # test before enabling cron
```

### Timeline to Production

| Week | Milestone |
|---|---|
| 1 | API access approved on all platforms; UTM taxonomy audit complete |
| 2 | Data pulls working + normalized schema in DuckDB |
| 3 | Anomaly detector + optimizer tuned against 8 weeks of history |
| 4 | PDF / HTML / Slack delivery working; shadow-run for 1 week |
| 5 | Go live — replaces manual Monday ritual |

### Cost Estimate (monthly)

| Item | Cost |
|---|---|
| Ad-platform APIs | $0 (free at this volume) |
| GA4 Data API | $0 |
| CRM API | Included in existing HubSpot/SFDC license |
| Claude API (narrative) | ~$2–5/month (≈ 4 runs × ~15k tokens) |
| Compute (Lambda / Render) | $0–10/month |
| PDF hosting (S3 / Vercel) | $0–5/month |
| **Total** | **~$10–20/month** |

---

## ROI Analysis

**Time saved:** ~9 hours/week × 52 weeks = **468 hours/year**
At a loaded Marketing Manager rate of $75/hr, that's **~$35,100/year** in reclaimed time alone.

**Revenue impact (the real win):**
Faster anomaly detection + data-driven budget shifts typically recover **3–8%** of paid-media spend that would otherwise be wasted. For a company spending $30k/week ($1.5M/year) on paid media, even a conservative 4% efficiency gain = **$60,000/year in recovered pipeline**, with zero additional headcount.

**Risk reduction:**
- No more manual copy-paste errors in board-facing numbers
- Campaigns that break (creative fatigue, disapproved ads, tracking failures) are caught within 7 days max instead of the current 14–30
- Leadership finally makes budget decisions on Monday at 8 AM — not Thursday afternoon

**Strategic upgrade:**
The Marketing Manager's role shifts from *"person who assembles the report"* to *"person who acts on the report."* That's how you turn a $90k coordinator role into a $140k strategist role — same person, same headcount.

---

## Variations & Extensions

- **Agency version:** add a `client_id` dimension to every row and generate one report per client automatically. Agencies managing 10+ clients save 40+ hours/week.
- **E-commerce version:** pull Shopify orders instead of CRM deals, use product-level ROAS and add SKU-level margin in the optimizer.
- **B2B enterprise:** add Bombora / 6sense intent data and layer account-level engagement scoring on top of ABM channels.
- **Creative-level diagnostics:** add image/video creative fatigue detection via Meta frequency + CTR decay curves, automatically propose which assets to refresh.

---

*Part of the [Agentic Workflows](https://github.com/heymarii/Agentic-Workflows) blueprint series — giving knowledge workers 10+ hours back every week.*
