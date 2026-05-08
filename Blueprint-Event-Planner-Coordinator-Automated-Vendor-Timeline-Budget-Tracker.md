# Blueprint: Event Planner / Coordinator — Automated Vendor, Timeline & Budget Tracker

> **Role:** Event Planner / Event Coordinator  
> **Task Automated:** Daily vendor follow-up tracking, timeline milestone management, budget reconciliation, guest RSVP aggregation, and day-of logistics coordination  
> **Time Saved:** 12–15 hours/week  
> **Difficulty:** Low–Medium (no coding required for core workflow)  
> **Tools Used:** Google Sheets / Airtable, Gmail / Outlook, Zapier or Make, Claude AI, Google Calendar

---

## The Problem

Event planners juggle dozens of moving pieces across every event — caterers, florists, AV techs, venues, photographers, rental companies — each with their own timelines, contracts, and payment schedules. On top of vendor management, planners manually track RSVPs, reconcile budgets against actuals, send timeline reminders, and compile day-of run sheets.

### What the Typical Day Looks Like (Without Automation)

| Task | Time Spent | Frequency |
|------|-----------|-----------|
| Chasing vendor confirmations via email/phone | 45 min | Daily |
| Updating master timeline spreadsheet | 30 min | Daily |
| Reconciling deposits, payments, and remaining balances | 40 min | Daily |
| Aggregating RSVPs from multiple sources (email, website, text) | 35 min | Daily |
| Building/updating day-of run sheets | 30 min | Per event |
| Sending timeline reminders to vendors and clients | 25 min | 2–3x/week |
| Compiling post-event wrap-up reports | 45 min | Per event |

**Total: 12–15 hours/week** on tasks that follow predictable patterns and rules.

### Why This Matters

Missed vendor follow-ups lead to no-shows. Untracked budget overruns eat into margins. Late timeline updates cause cascading coordination failures on event day. These aren't creative tasks — they're administrative overhead that keeps planners from doing what they do best: designing exceptional experiences.

---

## The Solution: AI-Powered Event Operations Autopilot

An automated daily workflow that:

1. **Scans incoming emails** for vendor responses, confirmations, and invoices
2. **Updates the master vendor tracker** with confirmation status, payment milestones, and outstanding items
3. **Flags overdue items** and auto-drafts follow-up emails for vendor and client reminders
4. **Reconciles the budget** by matching invoices/payments against contracted amounts
5. **Aggregates RSVPs** from connected sources into a single dashboard
6. **Generates a daily briefing** with today's deadlines, this week's milestones, budget health, and action items
7. **Produces a day-of run sheet** (for events within 72 hours) with minute-by-minute logistics

---

## Workflow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DAILY TRIGGER (7:00 AM)                          │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │   1. EMAIL SCANNER     │
              │                        │
              │ • Scan inbox for       │
              │   vendor replies       │
              │ • Extract invoice      │
              │   attachments          │
              │ • Identify RSVPs       │
              │ • Flag urgent items    │
              └───────────┬────────────┘
                          │
              ┌───────────┴────────────┐
              │                        │
              ▼                        ▼
┌──────────────────────┐  ┌──────────────────────┐
│ 2. VENDOR TRACKER    │  │ 3. RSVP AGGREGATOR   │
│    UPDATE            │  │                      │
│                      │  │ • Website form       │
│ • Confirmation       │  │ • Email replies      │
│   status → Updated   │  │ • Text responses     │
│ • Payment received   │  │ • Social messages    │
│   → Logged           │  │                      │
│ • New invoice        │  │ Dedup + categorize:  │
│   → Matched to       │  │ Attending / Declined │
│     contract line    │  │ / Pending / +1s      │
└──────────┬───────────┘  └──────────┬───────────┘
           │                         │
           ▼                         ▼
┌──────────────────────┐  ┌──────────────────────┐
│ 4. BUDGET            │  │ 5. TIMELINE          │
│    RECONCILIATION    │  │    MILESTONE CHECK   │
│                      │  │                      │
│ • Contract vs Actual │  │ • What's due today   │
│ • Deposits tracked   │  │ • What's overdue     │
│ • Remaining balances │  │ • What's due this    │
│ • Category spending  │  │   week               │
│ • Variance alerts    │  │ • Auto-draft         │
│   (>10% over)        │  │   reminder emails    │
└──────────┬───────────┘  └──────────┬───────────┘
           │                         │
           └───────────┬─────────────┘
                       │
                       ▼
          ┌────────────────────────┐
          │  6. DAILY BRIEFING     │
          │     GENERATOR          │
          │                        │
          │ • Today's action items │
          │ • Overdue follow-ups   │
          │ • Budget snapshot      │
          │ • RSVP count update    │
          │ • This week's preview  │
          │ • Risk flags           │
          └───────────┬────────────┘
                      │
           ┌──────────┴──────────┐
           │                     │
           ▼                     ▼
┌────────────────────┐ ┌────────────────────────┐
│ 7a. EMAIL BRIEFING │ │ 7b. DAY-OF RUN SHEET   │
│    TO PLANNER      │ │    (if event ≤72hrs)   │
│                    │ │                        │
│ Formatted daily    │ │ • Minute-by-minute     │
│ digest with        │ │   timeline             │
│ action buttons     │ │ • Vendor contact sheet │
│                    │ │ • Setup/strike plan    │
│                    │ │ • Emergency contacts   │
│                    │ │ • Weather backup plan  │
└────────────────────┘ └────────────────────────┘
```

---

## Implementation Guide

### Step 1: Set Up the Master Event Tracker (Google Sheets / Airtable)

Create a workbook with the following sheets/tables:

#### Sheet 1: Vendor Tracker

| Column | Description | Example |
|--------|------------|---------|
| Event Name | Event identifier | "Johnson Wedding 6/14" |
| Vendor Name | Company name | "Blooming Petals Florals" |
| Vendor Category | Type of service | Florist |
| Contact Name | Primary contact | Sarah Chen |
| Contact Email | Email address | sarah@bloomingpetals.com |
| Contact Phone | Phone number | (555) 234-5678 |
| Contract Amount | Agreed price | $3,200.00 |
| Deposit Paid | Amount deposited | $1,600.00 |
| Deposit Date | When deposit was sent | 2026-03-15 |
| Balance Due | Remaining amount | $1,600.00 |
| Balance Due Date | When final payment is due | 2026-06-07 |
| Confirmation Status | Confirmed / Pending / Unresponsive | Confirmed |
| Last Contact Date | Last communication | 2026-05-06 |
| Last Contact Method | Email / Phone / Text | Email |
| Notes | Special requirements | "Peonies substitution OK if unavailable" |
| Follow-Up Needed | Yes / No | No |
| Days Since Contact | Auto-calculated | 2 |

#### Sheet 2: Timeline & Milestones

| Column | Description | Example |
|--------|------------|---------|
| Event Name | Event identifier | "Johnson Wedding 6/14" |
| Milestone | Task description | "Final headcount to caterer" |
| Category | Vendor / Client / Internal | Vendor |
| Responsible Party | Who owns this | Planner |
| Due Date | Deadline | 2026-06-07 |
| Status | Complete / On Track / Overdue / At Risk | On Track |
| Days Until Due | Auto-calculated | 30 |
| Reminder Sent | Yes / No + date | No |
| Completion Date | When actually completed | — |
| Depends On | Prerequisite milestone | "Final RSVP count" |

#### Sheet 3: Budget Tracker

| Column | Description | Example |
|--------|------------|---------|
| Event Name | Event identifier | "Johnson Wedding 6/14" |
| Category | Budget category | Florals |
| Budgeted Amount | Planned spend | $3,500.00 |
| Contracted Amount | Actual contract | $3,200.00 |
| Paid to Date | Total paid | $1,600.00 |
| Remaining | Outstanding balance | $1,600.00 |
| Variance | Over/under budget | -$300.00 (under) |
| Variance % | Percentage | -8.6% |
| Invoice Numbers | Tracking | INV-2026-0341 |
| Payment Method | How paid | Credit Card |

#### Sheet 4: RSVP Tracker

| Column | Description | Example |
|--------|------------|---------|
| Event Name | Event identifier | "Johnson Wedding 6/14" |
| Guest Name | Full name | "Michael Torres" |
| Email | Contact email | mike.t@email.com |
| RSVP Status | Attending / Declined / Pending | Attending |
| Plus Ones | Number of additional guests | 1 |
| Meal Preference | Dietary selection | Vegetarian |
| Table Assignment | Seating group | Table 7 |
| Source | Where RSVP came from | Website Form |
| Date Received | When response came in | 2026-05-01 |
| Special Needs | Accessibility, allergies, etc. | Wheelchair accessible seating |

### Step 2: Configure Email Scanning Automation

**Using Zapier / Make:**

**Trigger:** New email received in designated event inbox (or labeled folder)

**Filters:** Apply keyword detection for:
- Vendor names (from Vendor Tracker)
- Invoice/payment keywords: "invoice," "receipt," "payment," "deposit," "confirmation"
- RSVP keywords: "attending," "RSVP," "accept," "decline," "regret"

**Actions:**
1. Forward email content to Claude AI for classification
2. Route to appropriate update automation (vendor update, RSVP log, or budget entry)

### Step 3: AI Classification & Extraction Prompt

Use this prompt with Claude to classify and extract information from incoming emails:

```
You are an event planning assistant. Analyze this email and return structured JSON.

EMAIL CONTENT:
{{email_body}}

SENDER: {{sender_email}}
SUBJECT: {{subject_line}}
DATE: {{received_date}}

CURRENT ACTIVE EVENTS:
{{active_events_list}}

Classify this email into ONE category:
- VENDOR_CONFIRMATION: Vendor confirming details, availability, or setup plans
- VENDOR_INVOICE: Invoice, receipt, or payment request
- VENDOR_QUESTION: Vendor asking for clarification or details
- RSVP_ACCEPT: Guest confirming attendance
- RSVP_DECLINE: Guest declining
- RSVP_QUESTION: Guest asking about event details
- CLIENT_UPDATE: Client communication about event changes
- GENERAL: Does not fit above categories

Return JSON:
{
  "category": "VENDOR_CONFIRMATION",
  "event_name": "matched event or null",
  "vendor_name": "matched vendor or null",
  "guest_name": "if RSVP, guest name",
  "plus_ones": 0,
  "meal_preference": "if mentioned",
  "invoice_amount": null,
  "invoice_number": null,
  "key_details": "2-3 sentence summary of important info",
  "action_required": true/false,
  "action_description": "what the planner needs to do, if anything",
  "urgency": "high/medium/low",
  "suggested_response": "draft reply if response needed"
}
```

### Step 4: Auto-Draft Follow-Up Emails

Configure Claude to draft follow-up emails for overdue vendor confirmations:

```
You are an event coordinator assistant. Draft a professional but warm follow-up email.

CONTEXT:
- Vendor: {{vendor_name}} ({{vendor_category}})
- Contact: {{contact_name}}
- Event: {{event_name}}
- Event Date: {{event_date}}
- Days since last contact: {{days_since_contact}}
- Outstanding item: {{what_we_need}}
- Previous communications summary: {{notes}}

RULES:
- First follow-up (3-5 days): Friendly check-in, assume they're busy
- Second follow-up (7-10 days): Slightly more direct, mention timeline
- Third follow-up (14+ days): Express concern, request response by specific date, mention backup plans
- Always include the specific information or confirmation needed
- Keep tone professional but personable — these are relationship-based businesses
- Include planner's name and contact info in signature

Draft the appropriate follow-up based on the timeline.
```

### Step 5: Daily Briefing Prompt

Run this every morning at 7:00 AM to generate the daily briefing:

```
You are an event operations AI. Generate today's daily briefing.

TODAY'S DATE: {{current_date}}

ACTIVE EVENTS DATA:
{{vendor_tracker_data}}
{{timeline_data}}
{{budget_data}}
{{rsvp_data}}

Generate a briefing with these sections:

## 🗓️ Today's Priority Actions
List items due today or overdue, ranked by urgency. Include who to contact and what's needed.

## ⚠️ Overdue Items (Requires Immediate Attention)
Any milestone or follow-up that is past due. Include days overdue and recommended action.

## 📊 Budget Health Check
For each active event:
- Total budget vs total contracted vs total paid
- Categories over budget (flag anything >10% over)
- Upcoming payment deadlines this week

## 👥 RSVP Snapshot
For each active event:
- Total invited vs responded vs pending
- Current headcount (including +1s)
- Response rate percentage
- If event is <14 days away and response rate <80%, flag for reminder push

## 📅 This Week's Milestones
Everything due in the next 7 days, grouped by event.

## 🚩 Risk Flags
Identify any of these situations:
- Vendor unresponsive for 7+ days with event <30 days away
- Budget category >15% over with no approved change order
- Critical milestone overdue with dependencies
- Headcount significantly different from estimate (>15% variance)
- Weather concerns for outdoor events within 5 days

## 📧 Recommended Emails to Send Today
List the follow-ups and reminders that should go out, with draft previews.

Format as a clean, scannable email suitable for mobile reading.
```

### Step 6: Day-Of Run Sheet Generator

For events within 72 hours, auto-generate a comprehensive run sheet:

```
You are an event logistics coordinator. Generate a detailed day-of run sheet.

EVENT DETAILS:
{{event_name}}
{{event_date}}
{{venue_name_and_address}}
{{event_start_time}}
{{event_end_time}}
{{expected_headcount}}

VENDOR INFORMATION:
{{all_vendor_details_with_contacts}}

TIMELINE MILESTONES FOR EVENT DAY:
{{day_of_milestones}}

CLIENT SPECIAL REQUESTS:
{{client_notes}}

Generate a run sheet with:

## Event Overview
Quick reference card: event name, date, venue, client, headcount, planner contact.

## Minute-by-Minute Timeline
From first vendor arrival through last vendor strike. Include:
- Exact times for each vendor arrival
- Setup completion deadlines
- Key ceremony/program moments
- Meal service times
- Transition points
- Breakdown/strike schedule

## Vendor Contact Sheet
Table with: vendor name, category, on-site contact name, cell phone, arrival time, setup location, special notes.

## Setup Diagram Notes
Room/space layout instructions for each area.

## Emergency Contacts & Backup Plans
- Venue emergency contact
- Nearest hospital/urgent care
- Backup plan for weather (if outdoor)
- Planner's emergency protocol

## Client VIP Notes
Special guests, seating considerations, dietary needs, accessibility requirements, surprises planned.

## Post-Event Checklist
Strike order, vendor payment reminders (tips, final balances), lost & found protocol, thank-you notes due.
```

---

## Example Output: Daily Briefing

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  EVENT OPERATIONS DAILY BRIEFING
  Thursday, May 8, 2026
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📋 ACTIVE EVENTS: 3
   • Johnson Wedding — June 14 (37 days)
   • TechCorp Annual Gala — May 23 (15 days)
   • Rivera Birthday (50th) — May 30 (22 days)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🗓️ TODAY'S PRIORITY ACTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. [URGENT] TechCorp Gala — Final AV requirements
   due to SoundWave Productions TODAY
   → Contact: Mike Reeves (mike@soundwavepro.com)
   → Need: Stage plot + microphone count for 3 speakers

2. [URGENT] TechCorp Gala — Confirm bar package upgrade
   with venue (Grand Meridian Ballroom)
   → Contact: Lisa Park (events@grandmeridian.com)
   → Client approved premium tier on 5/5, venue needs
     written confirmation + $1,200 deposit

3. [TODAY] Rivera 50th — Cake tasting appointment 2:00 PM
   → Sugar & Bloom Bakery, 142 Main St
   → Client attending — confirm ride/directions sent

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ OVERDUE ITEMS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔴 TechCorp Gala — Centerpiece mockup photos
   Vendor: Luxe Floral Design | 4 days overdue
   Last contact: May 4 (email, no response)
   → ACTION: Send 2nd follow-up (draft ready below)
   → ESCALATE if no response by May 9

🟡 Johnson Wedding — Linen color samples
   Vendor: Premier Party Rentals | 2 days overdue
   Last contact: May 6 (email, read receipt confirmed)
   → ACTION: Call today, samples needed for client
     meeting on May 12

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 BUDGET HEALTH CHECK
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TECHCORP ANNUAL GALA
  Budget: $45,000 | Contracted: $43,750 | Paid: $22,100
  Remaining: $21,650
  ⚠️ Bar/Beverage: 18% over budget ($5,900 vs $5,000)
     → Client-approved upgrade, change order signed
  ✅ All other categories within 5% of budget
  💳 Due this week: SoundWave deposit $2,400 (May 10)

JOHNSON WEDDING
  Budget: $62,000 | Contracted: $58,200 | Paid: $31,500
  Remaining: $26,700
  ✅ All categories on track
  💳 Due this week: None

RIVERA 50TH BIRTHDAY
  Budget: $18,000 | Contracted: $16,850 | Paid: $8,500
  Remaining: $8,350
  ✅ All categories on track
  💳 Due this week: Bakery deposit $450 (after tasting)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👥 RSVP SNAPSHOT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TECHCORP GALA (May 23 — 15 days away)
  Invited: 200 | Responded: 178 | Pending: 22
  Attending: 156 (+12 guests) = 168 total
  Declined: 22 | Response rate: 89%
  🟢 On track — final count due to caterer May 16

JOHNSON WEDDING (June 14 — 37 days away)
  Invited: 175 | Responded: 112 | Pending: 63
  Attending: 98 (+45 guests) = 143 total
  Declined: 14 | Response rate: 64%
  🟡 Below target — recommend reminder push for
     non-respondents (RSVP deadline: May 24)

RIVERA 50TH (May 30 — 22 days away)
  Invited: 60 | Responded: 48 | Pending: 12
  Attending: 44 (+6 guests) = 50 total
  Declined: 4 | Response rate: 80%
  🟢 On track

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📧 RECOMMENDED EMAILS TO SEND TODAY (3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. → Luxe Floral Design (2nd follow-up)
   Subject: Following up — centerpiece mockup photos
   [DRAFT READY — Review & Send]

2. → Grand Meridian Ballroom (bar upgrade confirmation)
   Subject: TechCorp Gala — Premium bar package
   confirmation
   [DRAFT READY — Review & Send]

3. → Johnson Wedding guest list (RSVP reminder)
   Subject: We'd love to celebrate with you! RSVP
   reminder for Sarah & David's wedding
   [DRAFT READY — Needs client approval first]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Example Output: Day-Of Run Sheet (Partial)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  DAY-OF RUN SHEET
  TechCorp Annual Gala — May 23, 2026
  Grand Meridian Ballroom | 168 Guests
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TIMELINE
────────
10:00 AM  Planner arrives, venue walkthrough
10:30 AM  Luxe Floral delivery & setup begins
11:00 AM  AV team (SoundWave) load-in starts
11:00 AM  Linen & table setup (Premier Rentals)
12:00 PM  Lighting rig complete — test run
12:30 PM  Floral centerpieces placed
 1:00 PM  *** LUNCH BREAK — crew ***
 1:30 PM  AV sound check with emcee
 2:00 PM  Catering team arrives (Harvest Table)
 2:30 PM  Bar setup begins
 3:00 PM  Photographer arrives for detail shots
 3:30 PM  Final walkthrough with venue manager
 4:00 PM  *** ALL SETUP COMPLETE — DOORS LOCKED ***
 5:00 PM  Planner returns, final check
 5:30 PM  Registration desk opens
 6:00 PM  Doors open — cocktail hour begins
 6:15 PM  Background music starts (DJ)
 7:00 PM  Guests seated — dinner service begins
 7:05 PM  CEO welcome remarks (5 min)
 7:10 PM  First course served
 7:45 PM  Second course / Award presentation
 8:30 PM  Dessert service + coffee
 8:45 PM  Keynote speaker (20 min)
 9:05 PM  Dance floor opens
10:30 PM  Last call at bar
10:45 PM  Thank you / farewell from CEO
11:00 PM  Event ends — guests depart
11:15 PM  Strike begins
12:00 AM  Venue clear

VENDOR CONTACT SHEET
──────────────────────
Venue: Grand Meridian | Lisa Park | (555) 100-2000
Catering: Harvest Table | Chef Dan | (555) 200-3000
Florals: Luxe Floral | Amy Lin | (555) 300-4000
AV: SoundWave | Mike Reeves | (555) 400-5000
Photo: Lens & Light | Jordan Ko | (555) 500-6000
Rentals: Premier Party | Tom Hayes | (555) 600-7000
DJ: BeatDrop Ent. | DJ Marcus | (555) 700-8000
```

---

## Setup Automation (Zapier / Make)

### Automation 1: Email Classification Pipeline

```
Trigger: New email in "Events" inbox label
  → Action 1: Send to Claude API for classification
  → Action 2: Route based on category:
    - VENDOR_*: Update Vendor Tracker sheet
    - RSVP_*: Update RSVP Tracker sheet
    - VENDOR_INVOICE: Update Budget Tracker sheet
  → Action 3: If action_required = true, add to Today's Actions
```

### Automation 2: Overdue Follow-Up Scanner

```
Trigger: Daily at 6:30 AM
  → Action 1: Query Vendor Tracker for rows where
    Days_Since_Contact > 3 AND Confirmation_Status = "Pending"
  → Action 2: Send to Claude API with follow-up prompt
  → Action 3: Create draft emails (NOT auto-send)
  → Action 4: Notify planner of drafts ready for review
```

### Automation 3: Daily Briefing

```
Trigger: Daily at 7:00 AM
  → Action 1: Pull all tracker data
  → Action 2: Send to Claude API with briefing prompt
  → Action 3: Email formatted briefing to planner
  → Action 4: If any event is ≤72 hours away:
    - Also generate day-of run sheet
    - Attach as PDF
```

### Automation 4: Budget Alert Monitor

```
Trigger: Any update to Budget Tracker sheet
  → Filter: Variance% > 10%
  → Action 1: Send Slack/email alert to planner
  → Action 2: Flag row in sheet with ⚠️
  → Action 3: Log alert in event notes
```

### Automation 5: RSVP Aggregator

```
Trigger: New form submission / incoming email with RSVP keywords
  → Action 1: Extract guest details via Claude
  → Action 2: Deduplicate against existing RSVP list
  → Action 3: Add/update RSVP Tracker
  → Action 4: If event <14 days away, update caterer headcount note
```

---

## ROI Analysis

### Time Savings Breakdown

| Task | Before | After | Weekly Savings |
|------|--------|-------|----------------|
| Vendor follow-up emails | 45 min/day | 10 min/day (review drafts) | 2.9 hrs |
| Timeline tracking & updates | 30 min/day | 5 min/day (review briefing) | 2.1 hrs |
| Budget reconciliation | 40 min/day | 5 min/day (review alerts) | 2.9 hrs |
| RSVP aggregation | 35 min/day | 0 min (fully automated) | 2.9 hrs |
| Day-of run sheet creation | 2 hrs/event | 15 min/event (review & customize) | 1.75 hrs* |
| Sending reminders | 25 min, 3x/week | 5 min (approve auto-drafts) | 1.0 hr |

*Averaged across typical event load of 2–3 events/month

**Total Weekly Savings: ~13.5 hours**

### Cost Analysis

| Component | Monthly Cost |
|-----------|-------------|
| Claude AI API (classification + drafting) | $15–30 |
| Zapier Professional Plan | $49 |
| Google Workspace (Sheets, Gmail) | $12 |
| **Total** | **$76–91/month** |

### Financial Impact

For an event planner billing at $75–150/hour or managing $50K+ events:

- **13.5 hours saved/week × $100/hr average = $1,350/week in recaptured billable time**
- **$5,400/month in value vs $91/month in costs = 59:1 ROI**
- **Fewer missed follow-ups = fewer vendor issues = better client satisfaction**
- **Accurate real-time budgets = fewer surprise overruns = protected margins**

---

## 5-Day Quick-Start Plan

### Day 1: Build the Foundation
- Create the Google Sheets workbook with all four tracker sheets
- Populate with your current active events, vendors, and milestones
- Set up formulas for Days_Since_Contact and Variance calculations

### Day 2: Connect Email Classification
- Set up Zapier/Make account
- Create the email trigger + Claude classification automation
- Test with 5–10 sample emails to validate accuracy
- Adjust classification prompt if needed

### Day 3: Activate Follow-Up & Reminder Automations
- Configure the overdue follow-up scanner
- Set up the daily briefing generation
- Test email draft quality and adjust prompts

### Day 4: Wire Up RSVP Aggregation & Budget Alerts
- Connect RSVP sources (website form, email filters)
- Configure budget variance alerting
- Test end-to-end with sample data

### Day 5: Go Live & Refine
- Run full daily cycle with real data
- Review briefing for accuracy and completeness
- Adjust thresholds (follow-up days, budget alert percentage)
- Generate your first automated day-of run sheet

---

## Customization Options

### For Wedding Planners
- Add bridal party contact tracking
- Include rehearsal dinner logistics as separate sub-event
- Track gift registry / thank-you note status
- Add ceremony-specific timeline (processional order, readings, music cues)

### For Corporate Event Planners
- Add sponsorship tracking and deliverables
- Include speaker/presenter management module
- Track registration tiers and badge printing queue
- Add post-event survey distribution automation

### For Non-Profit / Fundraiser Planners
- Add donor/sponsor acknowledgment tracking
- Include auction item management
- Track pledge vs collected amounts
- Add tax receipt generation workflow

### For Conference Organizers
- Add multi-track session scheduling
- Include speaker travel/hotel coordination
- Track exhibitor booth assignments and requirements
- Add attendee engagement metrics (session attendance, app usage)

---

## Advanced: Post-Event Wrap-Up Automation

After each event, trigger this prompt to generate a comprehensive debrief:

```
Generate a post-event wrap-up report for:
EVENT: {{event_name}}
DATE: {{event_date}}

FINAL NUMBERS:
- Budget: {{final_budget_data}}
- Attendance: {{final_attendance}}
- Vendor performance notes: {{vendor_notes}}

Include:
1. Executive summary (3-4 sentences)
2. Budget final reconciliation (budgeted vs actual per category)
3. Vendor scorecard (rating 1-5 on communication, quality,
   timeliness, value — based on notes and timeline adherence)
4. What went well (from planner notes + timeline data)
5. Improvement areas for next event
6. Client follow-up actions (final invoice, testimonial
   request, referral ask — with draft messages)
7. Recommended vendor re-hire list
```

This creates an institutional knowledge base that makes every future event better.

---

*Blueprint created: May 8, 2026 | Agentic Workflows Collection*  
*Estimated implementation time: 5 days | No coding required*
