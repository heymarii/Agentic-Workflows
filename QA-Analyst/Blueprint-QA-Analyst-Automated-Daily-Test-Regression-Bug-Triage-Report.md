# Blueprint: QA Analyst — Automated Daily Test & Regression Bug Triage Report

> **Role:** Quality Assurance (QA) Analyst  
> **Task Automated:** Daily test execution summary, regression bug triage, and defect trend reporting  
> **Time Saved:** ~8–12 hours/week  
> **Difficulty:** Medium  
> **Tools Used:** Jira API (or any issue tracker), CI/CD pipeline API (Jenkins/GitHub Actions/GitLab CI), Google Sheets or Excel, Claude AI, Slack/Teams webhook  

---

## The Problem

QA Analysts spend a significant chunk of every morning doing the same thing:

1. **Checking overnight CI/CD test results** — scrolling through pipeline logs to identify which tests passed, failed, or were flaky.
2. **Manually triaging new bugs** — reading through newly filed defects, classifying severity, assigning to dev teams, and flagging regressions vs. known issues.
3. **Compiling a daily status report** — pulling numbers from multiple sources (test management tool, issue tracker, CI dashboard) into a spreadsheet or email for stakeholders.
4. **Tracking regression trends** — comparing today's failures against yesterday's to spot new regressions before they snowball.

This workflow is tedious, error-prone (humans miss patterns in large datasets), and takes 1.5–2.5 hours every single morning. Multiply that across a team of QA analysts and the cost is enormous.

---

## The Solution: An Automated Daily QA Report Pipeline

An agentic workflow that runs automatically at 7:00 AM every workday and delivers a complete, actionable QA status report before the standup meeting.

---

## Workflow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AUTOMATED QA REPORT PIPELINE                     │
│                    Runs Daily at 7:00 AM                            │
└─────────────────────────────────────────────────────────────────────┘

  ┌──────────────┐     ┌──────────────┐     ┌──────────────────────┐
  │  CI/CD API   │     │  Jira / Issue │     │  Test Management     │
  │  (Jenkins /  │     │  Tracker API  │     │  Tool (TestRail /    │
  │  GitHub      │     │               │     │  Zephyr / qTest)     │
  │  Actions)    │     │               │     │                      │
  └──────┬───────┘     └──────┬────────┘     └──────────┬───────────┘
         │                    │                         │
         ▼                    ▼                         ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │                     STEP 1: DATA COLLECTION                      │
  │                                                                  │
  │  • Pull last 24h pipeline run results (pass/fail/skip counts)   │
  │  • Pull newly created bugs (last 24h)                           │
  │  • Pull test execution results by suite/module                  │
  │  • Pull historical data (last 7 days) for trend analysis        │
  └──────────────────────────────┬───────────────────────────────────┘
                                 │
                                 ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │                     STEP 2: AI-POWERED TRIAGE                    │
  │                                                                  │
  │  Claude AI analyzes the collected data:                          │
  │                                                                  │
  │  • Classify each failed test as:                                │
  │    → NEW REGRESSION (not failed yesterday)                      │
  │    → KNOWN FAILURE (linked to existing bug)                     │
  │    → FLAKY TEST (intermittent pattern detected)                 │
  │    → ENVIRONMENT ISSUE (infra-related, not code)                │
  │                                                                  │
  │  • Classify each new bug by:                                    │
  │    → Suggested severity (Critical/High/Medium/Low)              │
  │    → Suggested component/team assignment                        │
  │    → Whether it matches a known regression pattern              │
  │                                                                  │
  │  • Generate trend insights:                                     │
  │    → "Test pass rate dropped 4% since Monday — 3 new            │
  │       regressions in the Payments module after PR #2847"        │
  └──────────────────────────────┬───────────────────────────────────┘
                                 │
                                 ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │                 STEP 3: REPORT GENERATION                        │
  │                                                                  │
  │  Produce three outputs:                                         │
  │                                                                  │
  │  📊 Executive Summary (Slack/Teams message)                     │
  │     → Pass rate, new regressions, blocker count                 │
  │                                                                  │
  │  📋 Detailed Report (Markdown/HTML)                             │
  │     → Full breakdown by module, trend charts, bug list          │
  │                                                                  │
  │  📈 Spreadsheet (auto-updated Google Sheet)                     │
  │     → Historical tracking, charts, pivot tables                 │
  └──────────────────────────────┬───────────────────────────────────┘
                                 │
                                 ▼
  ┌──────────────────────────────────────────────────────────────────┐
  │                    STEP 4: DISTRIBUTION                          │
  │                                                                  │
  │  • Post executive summary to #qa-daily Slack channel            │
  │  • Auto-update Jira bugs with triage suggestions (as comments)  │
  │  • Email detailed report to QA Lead & Engineering Manager       │
  │  • Update shared Google Sheet with today's data row             │
  └──────────────────────────────────────────────────────────────────┘
```

---

## Implementation

### Step 1: Data Collection Script

```python
"""
qa_data_collector.py
Pulls test results and bug data from CI/CD and issue tracker APIs.
"""

import requests
import json
from datetime import datetime, timedelta

# ── Configuration ──
JIRA_BASE_URL = "https://your-org.atlassian.net"
JIRA_API_TOKEN = "YOUR_JIRA_API_TOKEN"
JIRA_EMAIL = "qa-bot@yourcompany.com"
JIRA_PROJECT = "PROJ"

JENKINS_BASE_URL = "https://jenkins.yourcompany.com"
JENKINS_API_TOKEN = "YOUR_JENKINS_TOKEN"
JENKINS_JOB = "nightly-regression-suite"

LOOKBACK_HOURS = 24
TREND_DAYS = 7


def fetch_jenkins_results():
    """Fetch the last 24h of test execution results from Jenkins."""
    url = f"{JENKINS_BASE_URL}/job/{JENKINS_JOB}/lastBuild/api/json"
    params = {"tree": "result,timestamp,duration,actions[totalCount,failCount,skipCount]"}
    
    resp = requests.get(url, auth=("admin", JENKINS_API_TOKEN), params=params)
    resp.raise_for_status()
    build = resp.json()
    
    test_action = next(
        (a for a in build.get("actions", []) if "totalCount" in a), {}
    )
    
    return {
        "build_result": build.get("result", "UNKNOWN"),
        "timestamp": datetime.fromtimestamp(build["timestamp"] / 1000).isoformat(),
        "duration_minutes": round(build.get("duration", 0) / 60000, 1),
        "total_tests": test_action.get("totalCount", 0),
        "failed_tests": test_action.get("failCount", 0),
        "skipped_tests": test_action.get("skipCount", 0),
        "passed_tests": (
            test_action.get("totalCount", 0)
            - test_action.get("failCount", 0)
            - test_action.get("skipCount", 0)
        ),
    }


def fetch_jira_new_bugs():
    """Fetch bugs created in the last 24 hours."""
    since = (datetime.now() - timedelta(hours=LOOKBACK_HOURS)).strftime("%Y-%m-%d %H:%M")
    jql = (
        f'project = {JIRA_PROJECT} AND issuetype = Bug '
        f'AND created >= "{since}" ORDER BY created DESC'
    )
    
    url = f"{JIRA_BASE_URL}/rest/api/3/search"
    headers = {"Accept": "application/json"}
    params = {"jql": jql, "maxResults": 100, "fields": "summary,priority,status,components,labels,created,assignee"}
    
    resp = requests.get(url, headers=headers, auth=(JIRA_EMAIL, JIRA_API_TOKEN), params=params)
    resp.raise_for_status()
    
    bugs = []
    for issue in resp.json().get("issues", []):
        fields = issue["fields"]
        bugs.append({
            "key": issue["key"],
            "summary": fields["summary"],
            "priority": fields.get("priority", {}).get("name", "Unset"),
            "status": fields.get("status", {}).get("name", "Open"),
            "components": [c["name"] for c in fields.get("components", [])],
            "labels": fields.get("labels", []),
            "created": fields.get("created", ""),
            "assignee": (fields.get("assignee") or {}).get("displayName", "Unassigned"),
        })
    
    return bugs


def fetch_historical_pass_rates(days=TREND_DAYS):
    """Fetch pass rates from the last N days for trend analysis."""
    url = f"{JENKINS_BASE_URL}/job/{JENKINS_JOB}/api/json"
    params = {"tree": f"builds[number,result,timestamp,actions[totalCount,failCount,skipCount]]{{0,{days}}}"}
    
    resp = requests.get(url, auth=("admin", JENKINS_API_TOKEN), params=params)
    resp.raise_for_status()
    
    history = []
    for build in resp.json().get("builds", []):
        test_action = next(
            (a for a in build.get("actions", []) if "totalCount" in a), {}
        )
        total = test_action.get("totalCount", 0)
        failed = test_action.get("failCount", 0)
        if total > 0:
            history.append({
                "build_number": build["number"],
                "date": datetime.fromtimestamp(build["timestamp"] / 1000).strftime("%Y-%m-%d"),
                "pass_rate": round(((total - failed) / total) * 100, 1),
                "total": total,
                "failed": failed,
            })
    
    return history


def collect_all_data():
    """Main collection function — returns structured data for AI analysis."""
    data = {
        "collected_at": datetime.now().isoformat(),
        "test_results": fetch_jenkins_results(),
        "new_bugs": fetch_jira_new_bugs(),
        "historical_trend": fetch_historical_pass_rates(),
    }
    
    with open("qa_daily_data.json", "w") as f:
        json.dump(data, f, indent=2)
    
    print(f"Collected: {data['test_results']['total_tests']} tests, "
          f"{len(data['new_bugs'])} new bugs, "
          f"{len(data['historical_trend'])} days of history")
    
    return data


if __name__ == "__main__":
    collect_all_data()
```

### Step 2: AI-Powered Triage Prompt

```text
SYSTEM PROMPT — QA Bug Triage & Test Analysis Agent
=====================================================

You are an expert QA analyst AI assistant. You receive daily test execution
data and newly filed bugs, and you produce an actionable triage report.

INPUT DATA FORMAT:
You will receive a JSON object with:
- test_results: Today's CI/CD test run summary
- new_bugs: Array of bugs created in the last 24 hours
- historical_trend: Pass rates from the last 7 days

YOUR TASKS:

1. TEST FAILURE CLASSIFICATION
   For each category of failure, identify the count and list specific tests:
   - NEW REGRESSION: Tests that passed yesterday but fail today
   - KNOWN FAILURE: Tests linked to existing open bugs
   - FLAKY TEST: Tests with intermittent pass/fail pattern (>2 flips in 7 days)
   - ENVIRONMENT ISSUE: Failures caused by infra (timeouts, connection errors)

2. BUG TRIAGE
   For each new bug, provide:
   - Suggested severity (Critical / High / Medium / Low) with reasoning
   - Suggested component/team assignment
   - Whether it appears related to a recent code change
   - Whether it matches any known regression pattern

3. TREND ANALYSIS
   - Calculate pass rate trend (improving, declining, stable)
   - Identify modules with increasing failure rates
   - Flag any concerning patterns (e.g., "3 new failures in Auth module
     coincide with PR #2847 merged yesterday")

4. EXECUTIVE SUMMARY
   Write a 3-5 sentence summary suitable for a Slack message:
   - Overall health (green/yellow/red)
   - Key numbers (pass rate, new regressions, blockers)
   - Top action item

OUTPUT FORMAT: Return a structured JSON with sections for each task above.
```

### Step 3: Report Generation & Distribution

```python
"""
qa_report_generator.py
Takes AI-analyzed data and produces formatted reports for distribution.
"""

import json
import requests
from datetime import datetime


SLACK_WEBHOOK_URL = "https://hooks.slack.com/services/YOUR/WEBHOOK/URL"


def generate_slack_summary(analysis: dict) -> str:
    """Generate a concise Slack message from the AI analysis."""
    summary = analysis.get("executive_summary", {})
    health = summary.get("health_status", "unknown").upper()
    
    health_emoji = {"GREEN": "🟢", "YELLOW": "🟡", "RED": "🔴"}.get(health, "⚪")
    
    test_results = analysis.get("test_results_summary", {})
    pass_rate = test_results.get("pass_rate", "N/A")
    new_regressions = test_results.get("new_regressions_count", 0)
    blockers = test_results.get("blocker_count", 0)
    
    message = f"""
{health_emoji} *Daily QA Report — {datetime.now().strftime('%A, %B %d, %Y')}*

*Overall Health:* {health}
*Pass Rate:* {pass_rate}% (7-day avg: {test_results.get('weekly_avg', 'N/A')}%)
*New Regressions:* {new_regressions}
*Blockers:* {blockers}
*New Bugs Filed:* {test_results.get('new_bugs_count', 0)}

*Top Action Items:*
"""
    
    for i, item in enumerate(summary.get("action_items", [])[:3], 1):
        message += f"  {i}. {item}\n"
    
    if analysis.get("trend_alert"):
        message += f"\n⚠️ *Trend Alert:* {analysis['trend_alert']}\n"
    
    message += f"\n📋 <{analysis.get('full_report_url', '#')}|View Full Report>"
    
    return message


def generate_detailed_markdown(analysis: dict) -> str:
    """Generate a detailed Markdown report."""
    md = f"# QA Daily Report — {datetime.now().strftime('%Y-%m-%d')}\n\n"
    
    # Summary section
    md += "## Executive Summary\n\n"
    md += analysis.get("executive_summary", {}).get("narrative", "") + "\n\n"
    
    # Test Results Breakdown
    md += "## Test Results Breakdown\n\n"
    md += "| Category | Count | Details |\n"
    md += "|----------|------:|--------|\n"
    
    categories = analysis.get("failure_classification", {})
    for cat, data in categories.items():
        md += f"| {cat} | {data.get('count', 0)} | {data.get('summary', '')} |\n"
    
    # New Bug Triage
    md += "\n## New Bug Triage\n\n"
    for bug in analysis.get("bug_triage", []):
        severity_icon = {
            "Critical": "🔴", "High": "🟠", "Medium": "🟡", "Low": "🟢"
        }.get(bug.get("suggested_severity", ""), "⚪")
        
        md += f"### {severity_icon} {bug['key']}: {bug['summary']}\n\n"
        md += f"- **Suggested Severity:** {bug.get('suggested_severity', 'Unset')}\n"
        md += f"- **Suggested Team:** {bug.get('suggested_team', 'Unassigned')}\n"
        md += f"- **Regression?** {bug.get('is_regression', 'Unknown')}\n"
        md += f"- **Reasoning:** {bug.get('reasoning', '')}\n\n"
    
    # Trend Analysis
    md += "## 7-Day Trend\n\n"
    md += "| Date | Pass Rate | Failed | Total |\n"
    md += "|------|----------:|-------:|------:|\n"
    for day in analysis.get("trend_data", []):
        md += f"| {day['date']} | {day['pass_rate']}% | {day['failed']} | {day['total']} |\n"
    
    return md


def post_to_slack(message: str):
    """Post the summary to Slack."""
    payload = {"text": message, "unfurl_links": False}
    resp = requests.post(SLACK_WEBHOOK_URL, json=payload)
    resp.raise_for_status()
    print("Slack message posted successfully.")


def run_report_pipeline(analysis_file="qa_analysis.json"):
    """Main report pipeline."""
    with open(analysis_file) as f:
        analysis = json.load(f)
    
    # Generate outputs
    slack_msg = generate_slack_summary(analysis)
    detailed_md = generate_detailed_markdown(analysis)
    
    # Save detailed report
    report_path = f"qa_report_{datetime.now().strftime('%Y%m%d')}.md"
    with open(report_path, "w") as f:
        f.write(detailed_md)
    print(f"Detailed report saved: {report_path}")
    
    # Post to Slack
    post_to_slack(slack_msg)
    
    return {"slack_message": slack_msg, "report_path": report_path}


if __name__ == "__main__":
    run_report_pipeline()
```

### Step 4: Cron / Scheduler Setup

```bash
# Add to crontab (runs at 7:00 AM Mon-Fri)
# crontab -e
0 7 * * 1-5 cd /opt/qa-automation && python qa_data_collector.py && python qa_triage_ai.py && python qa_report_generator.py >> /var/log/qa_report.log 2>&1
```

Alternatively, use **GitHub Actions** for a hosted solution:

```yaml
# .github/workflows/daily-qa-report.yml
name: Daily QA Report

on:
  schedule:
    - cron: '0 12 * * 1-5'  # 7 AM EST (UTC-5)
  workflow_dispatch:

jobs:
  generate-report:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: pip install requests anthropic

      - name: Collect QA Data
        env:
          JIRA_API_TOKEN: ${{ secrets.JIRA_API_TOKEN }}
          JENKINS_API_TOKEN: ${{ secrets.JENKINS_API_TOKEN }}
        run: python qa_data_collector.py

      - name: AI Triage & Analysis
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: python qa_triage_ai.py

      - name: Generate & Distribute Report
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
        run: python qa_report_generator.py
```

---

## Example Output: Slack Summary

```
🟡 Daily QA Report — Friday, May 16, 2026

Overall Health: YELLOW
Pass Rate: 94.2% (7-day avg: 96.8%)
New Regressions: 3
Blockers: 1
New Bugs Filed: 7

Top Action Items:
  1. BLOCKER: PROJ-4521 — Payment gateway timeout on checkout (Payments team)
  2. Investigate 3 new regressions in Auth module after PR #2847 merged Thu
  3. Mark 4 flaky tests in Search suite for stabilization sprint

⚠️ Trend Alert: Pass rate dropped 2.6% over 3 days. Auth + Payments modules account for 80% of new failures.

📋 View Full Report
```

---

## Example Output: Detailed Report (excerpt)

### New Bug Triage

#### 🔴 PROJ-4521: Payment gateway timeout during checkout flow
- **Suggested Severity:** Critical
- **Suggested Team:** Payments
- **Regression?** Yes — checkout tests passed until build #1847
- **Reasoning:** Affects revenue-critical path. Timeout errors started after the Stripe SDK update in PR #2851. Recommend immediate rollback investigation.

#### 🟠 PROJ-4522: User profile photo upload returns 500 on files > 5MB
- **Suggested Severity:** High
- **Suggested Team:** User Platform
- **Regression?** No — likely pre-existing, exposed by new test coverage
- **Reasoning:** Error only on large files. S3 multipart upload config may have a size limit mismatch.

#### 🟡 PROJ-4523: Search results pagination shows duplicate items on page 2
- **Suggested Severity:** Medium
- **Suggested Team:** Search/Discovery
- **Regression?** Unknown — inconsistent reproduction
- **Reasoning:** May be Elasticsearch cursor issue. Similar to PROJ-3891 (resolved). Recommend checking if fix regressed.

---

## Why This Should Be Implemented

| Metric | Before Automation | After Automation |
|--------|------------------:|----------------:|
| Morning triage time | 1.5–2.5 hrs/day | 15 min review |
| Bugs missed in triage | ~10–15% | <2% |
| Regression detection speed | Hours (manual review) | Minutes (automated) |
| Stakeholder report delivery | 10:30 AM (after standup) | 7:15 AM (before standup) |
| Weekly QA analyst hours saved | 0 | 8–12 hours |
| Consistency of reporting | Varies by analyst | Standardized |

### ROI Calculation

- **QA Analyst hourly cost:** ~$45/hour
- **Hours saved per week:** 10 hours (conservative)
- **Weekly savings:** $450/week per analyst
- **Annual savings (1 analyst):** ~$23,400
- **Team of 4 analysts:** ~$93,600/year
- **Implementation cost:** ~40 hours of engineering time (~$6,000)
- **Payback period:** Less than 1 month

---

## Getting Started Checklist

- [ ] Set up API access for your CI/CD tool (Jenkins/GitHub Actions/GitLab CI)
- [ ] Create a Jira API token with read access to your project
- [ ] Get an Anthropic API key for Claude
- [ ] Configure Slack webhook for your QA channel
- [ ] Deploy the scripts to a server or GitHub Actions
- [ ] Set up the cron schedule
- [ ] Run a test execution and validate the output
- [ ] Share the first report with your team and gather feedback

---

*Blueprint created: May 16, 2026*  
*Part of the Agentic Workflows collection — automating 10+ hours/week for every role.*
