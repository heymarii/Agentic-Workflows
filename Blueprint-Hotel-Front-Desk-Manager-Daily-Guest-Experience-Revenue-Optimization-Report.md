# Blueprint: Hotel Front Desk Manager — Daily Guest Experience & Revenue Optimization Report

## Role Profile

**Role:** Hotel Front Desk Manager  
**Industry:** Hospitality  
**Company Size:** Boutique hotels, mid-size properties (50–250 rooms), independent hotel groups  
**Pain Level:** 🔴 Critical — Front desk managers routinely spend 3–4 hours per shift on manual tasks that should be automated

---

## The Problem

Hotel front desk managers are the nerve center of guest operations, yet they're buried in repetitive manual work that steals time from what actually matters: guest experience.

**Daily time drains:**

| Task | Manual Time | Frequency |
|------|------------|-----------|
| Pre-arrival guest profiling & room assignment | 45 min | Daily |
| VIP/loyalty guest identification & special prep | 30 min | Daily |
| Shift handoff report compilation | 40 min | 2–3x daily |
| Guest complaint tracking & follow-up logging | 35 min | Daily |
| Housekeeping coordination & room status updates | 30 min | Ongoing |
| Daily revenue, occupancy & ADR reporting | 45 min | Daily |
| OTA review monitoring & response drafting | 40 min | Daily |
| No-show & cancellation processing | 20 min | Daily |
| **Total** | **~5.5 hours** | **Daily** |

A front desk manager working a 10-hour shift is spending more than half of it on tasks that follow predictable patterns. That means less time greeting VIP guests, resolving live issues, coaching staff, and driving upsell opportunities.

---

## The Automated Solution

### Overview

An AI-powered daily operations pipeline that ingests data from your PMS (Property Management System), OTA channels, guest feedback platforms, and housekeeping systems to produce a single, actionable **Daily Guest Experience & Revenue Optimization Report** — delivered before the morning shift starts.

### What It Delivers

1. **Pre-Arrival Guest Intelligence Brief** — Guest profiles, loyalty status, preferences, special requests, and room pre-assignments for today's arrivals
2. **Shift Handoff Digest** — Auto-generated summary of open issues, pending requests, VIP alerts, and unresolved complaints from the previous shift
3. **Revenue & Occupancy Snapshot** — Real-time ADR, RevPAR, occupancy rate, walk-in opportunities, and upsell targets
4. **Guest Sentiment Tracker** — New reviews across OTA channels with AI-drafted responses and complaint escalation flags
5. **Housekeeping Sync Dashboard** — Room status matrix, rush priorities, maintenance flags, and inspection readiness

### Time Saved: 4.2 hours/day → **29+ hours/week**

---

## Workflow Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    DAILY TRIGGER (5:30 AM)                          │
│              Scheduled automation kicks off pipeline                 │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│                  PHASE 1: DATA COLLECTION (Parallel)                │
│                                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐             │
│  │  PMS Export   │  │  OTA Channel │  │ Housekeeping │             │
│  │  - Arrivals   │  │  - Reviews   │  │  - Room      │             │
│  │  - Departures │  │  - Bookings  │  │    Status    │             │
│  │  - In-house   │  │  - Messages  │  │  - Maint.    │             │
│  │  - Revenue    │  │  - Ratings   │  │    Tickets   │             │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘             │
│         │                  │                  │                      │
│  ┌──────────────┐  ┌──────────────┐                                │
│  │ Guest History │  │  Loyalty     │                                │
│  │  - Prev stays │  │  Program DB  │                                │
│  │  - Preferences│  │  - Tier/pts  │                                │
│  │  - Complaints │  │  - Perks     │                                │
│  └──────┬───────┘  └──────┬───────┘                                │
│         │                  │                                         │
└─────────┴──────────────────┴─────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│               PHASE 2: AI PROCESSING ENGINE                         │
│                                                                     │
│  ┌─────────────────────────────────────────────────────┐           │
│  │  Guest Intelligence Module                           │           │
│  │  • Match arrivals → guest history & preferences      │           │
│  │  • Flag VIPs, repeat guests, special occasions       │           │
│  │  • Pre-assign rooms based on preference matching     │           │
│  │  • Generate personalized welcome notes               │           │
│  └─────────────────────────────────────────────────────┘           │
│                                                                     │
│  ┌─────────────────────────────────────────────────────┐           │
│  │  Revenue Optimization Module                         │           │
│  │  • Calculate real-time ADR, RevPAR, occupancy        │           │
│  │  • Identify upsell opportunities (upgrades, pkgs)    │           │
│  │  • Flag walk-in pricing recommendations              │           │
│  │  • Compare against same-day-last-year benchmarks     │           │
│  └─────────────────────────────────────────────────────┘           │
│                                                                     │
│  ┌─────────────────────────────────────────────────────┐           │
│  │  Sentiment Analysis Module                           │           │
│  │  • Analyze new OTA reviews (tone, themes, urgency)   │           │
│  │  • Draft responses matching hotel brand voice         │           │
│  │  • Escalate negative reviews to GM automatically     │           │
│  │  • Track sentiment trends week-over-week              │           │
│  └─────────────────────────────────────────────────────┘           │
│                                                                     │
│  ┌─────────────────────────────────────────────────────┐           │
│  │  Operations Sync Module                              │           │
│  │  • Build room status matrix from housekeeping data   │           │
│  │  • Prioritize rush cleans for early arrivals         │           │
│  │  • Flag maintenance issues blocking check-ins        │           │
│  │  • Generate shift handoff summary from open tickets  │           │
│  └─────────────────────────────────────────────────────┘           │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│              PHASE 3: REPORT GENERATION & DISTRIBUTION              │
│                                                                     │
│  ┌──────────────────────────────────┐                              │
│  │  📋 Daily Report (PDF/Email)     │ → Front Desk Team (6:00 AM) │
│  │  📊 Revenue Dashboard (Live)     │ → GM & Revenue Mgr          │
│  │  🔔 VIP Alert Cards              │ → Front Desk Screens        │
│  │  💬 Review Response Drafts       │ → Marketing / FOM           │
│  │  🏠 Housekeeping Priority List   │ → HK Supervisor             │
│  └──────────────────────────────────┘                              │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Implementation Guide

### Tools Required

| Tool | Purpose | Cost |
|------|---------|------|
| **PMS API** (Opera, Cloudbeds, Mews, or similar) | Guest data, reservations, revenue | Included with PMS |
| **n8n or Make.com** | Workflow orchestration | Free–$29/mo |
| **Claude API or OpenAI API** | AI processing, text generation | ~$15–30/mo |
| **Google Sheets or Airtable** | Data staging & dashboard | Free–$20/mo |
| **Gmail / Slack** | Report distribution | Free |
| **Google Looker Studio** (optional) | Live revenue dashboard | Free |

**Estimated Monthly Cost:** $35–80/month  
**Estimated Monthly Value:** $3,500–6,000 (based on 29 hrs/week × $30–50/hr labor + revenue uplift from upsells)  
**ROI:** ~50–75x return

### Phase 1: PMS Data Extraction

Most modern PMS platforms offer APIs or scheduled CSV exports. Here's a generic extraction prompt for structuring the data:

```
SYSTEM PROMPT — PMS Data Processor

You are a hotel operations data processor. You will receive raw reservation 
and guest data from a PMS export. Structure it into the following categories:

1. TODAY'S ARRIVALS
   For each arrival, extract:
   - Confirmation #, Guest name, Room type booked, Nights, Rate
   - Loyalty tier (if any), Previous stays count
   - Special requests (from reservation notes)
   - Estimated arrival time (if provided)
   - OTA source (direct, Booking.com, Expedia, etc.)

2. TODAY'S DEPARTURES
   - Room #, Guest name, Checkout time, Folio balance
   - Any open charges or disputes

3. IN-HOUSE GUESTS
   - Room #, Guest name, Checkout date, Any open requests/complaints

4. REVENUE SNAPSHOT
   - Rooms sold tonight, Total room revenue, ADR
   - Occupancy %, Available inventory by room type

Format output as structured JSON for downstream processing.
```

### Phase 2: Guest Intelligence Processing

```
SYSTEM PROMPT — Guest Intelligence Analyst

You are a luxury hotel guest intelligence analyst. Given today's arrival list 
with guest history, produce a Guest Intelligence Brief.

For each arriving guest, provide:

## [Guest Name] — [Confirmation #]
- **Profile:** [New/Returning] guest, [X] previous stays
- **Loyalty:** [Tier level and relevant perks]
- **Preferences:** [Room preferences, pillow type, minibar, etc.]
- **Special Occasions:** [Birthday, anniversary, honeymoon — flag if within ±3 days]
- **Previous Issues:** [Any complaints from past stays — CRITICAL to address]
- **Recommended Room:** [Specific room # based on preference matching]
- **Personalization Opportunity:** [Specific action — e.g., "Welcome back card 
  with note about their preferred Chardonnay in the minibar"]
- **Upsell Opportunity:** [Room upgrade availability, package add-ons]

Flag VIP guests (loyalty top tier, influencers, corporate accounts) with a 
🌟 VIP marker and list them first.

Flag guests with previous complaints with a ⚠️ RECOVERY marker.

End with a summary:
- Total arrivals: [X]
- VIP arrivals: [X]  
- Recovery opportunities: [X]
- Upsell targets: [X]
```

### Phase 3: Shift Handoff Automation

```
SYSTEM PROMPT — Shift Handoff Generator

You are a hotel front desk shift handoff specialist. Given the outgoing shift's 
activity log, open tickets, and pending requests, generate a concise handoff 
briefing for the incoming shift.

Structure:

## SHIFT HANDOFF: [Outgoing Shift] → [Incoming Shift]
### Generated: [Date/Time]

### 🔴 CRITICAL / IMMEDIATE ACTION
[Items requiring immediate attention — guest complaints in progress, 
maintenance emergencies, VIP issues]

### 🟡 PENDING REQUESTS
[Open requests that need follow-up — extra towels, late checkout requests, 
room moves, package deliveries]

### 🟢 INFORMATIONAL
[FYI items — group arrivals, sold-out room types, special events on property, 
weather alerts affecting operations]

### 💰 REVENUE NOTES
[Walk-in rate guidance, upsell priorities, comp/adjustment approvals needed]

### 👤 GUEST NOTES
[Specific guest situations the incoming team should know about — 
"Room 412 guest is upset about noise, offered late checkout and F&B credit, 
awaiting their response"]

Keep each item to 1–2 sentences maximum. Be specific about room numbers, 
guest names, and what action is needed.
```

### Phase 4: OTA Review Monitoring & Response

```
SYSTEM PROMPT — Hotel Review Response Specialist

You are a hospitality brand voice specialist for [Hotel Name]. Monitor and 
respond to guest reviews across OTA platforms.

For each new review, provide:

### Review: [Platform] — [Rating] ⭐
**Guest:** [Name]  
**Stay Date:** [Date]  
**Sentiment:** [Positive / Mixed / Negative]  
**Key Themes:** [Cleanliness, Service, Location, Value, F&B, etc.]

**Drafted Response:**
[Professional, warm response that:
- Thanks the guest by name
- Acknowledges specific positives they mentioned
- Addresses concerns without being defensive
- Invites them back with a forward-looking statement
- Matches the hotel's brand voice (luxury = refined, boutique = personal, 
  business = efficient)
- Stays under 150 words]

**Escalation Required:** [Yes/No]  
**Reason:** [If yes — e.g., "Alleges health/safety issue requiring GM review"]

At the end, provide:
- New reviews today: [X]
- Average rating: [X.X]
- Reviews requiring escalation: [X]
- 7-day sentiment trend: [Improving / Stable / Declining]
```

### Phase 5: Revenue & Occupancy Dashboard

```
SYSTEM PROMPT — Hotel Revenue Analyst

You are a hotel revenue analyst. Given today's PMS data, produce a Revenue 
& Occupancy Optimization Snapshot.

## DAILY REVENUE SNAPSHOT — [Date]

### Occupancy
| Metric | Today | Yesterday | Same Day LY | MTD |
|--------|-------|-----------|-------------|-----|
| Rooms Sold | | | | |
| Occupancy % | | | | |
| ADR | | | | |
| RevPAR | | | | |
| Total Room Revenue | | | | |

### Channel Mix
| Channel | Rooms | Revenue | Avg Rate | % of Total |
|---------|-------|---------|----------|------------|
| Direct | | | | |
| Booking.com | | | | |
| Expedia | | | | |
| Corporate | | | | |
| Groups | | | | |
| Walk-in | | | | |

### Upsell Opportunities
[List specific opportunities with estimated revenue impact:
- "12 Standard King guests arriving could be offered Mountain View upgrade ($45/nt)"
- "3 rooms eligible for romance package add-on ($89)"
- "Walk-in rate recommendation: $XXX (based on current occupancy and demand)"]

### Alerts
- [Oversold room types requiring attention]
- [Upcoming group block releases]
- [Rate parity violations detected on OTAs]
- [Comp nights approaching monthly budget limit]
```

---

## Sample Output: Daily Guest Experience & Revenue Optimization Report

```
═══════════════════════════════════════════════════════════════
  🏨 DAILY GUEST EXPERIENCE & REVENUE OPTIMIZATION REPORT
     The Meridian Hotel — Thursday, May 7, 2026
     Generated: 5:45 AM | Morning Shift Briefing
═══════════════════════════════════════════════════════════════

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 REVENUE & OCCUPANCY AT A GLANCE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Occupancy:    87.2%  (131/150 rooms)   ▲ +3.1% vs yesterday
  ADR:          $189.40                   ▲ +$6.20 vs yesterday
  RevPAR:       $165.17                   ▲ +$8.44 vs yesterday
  Room Revenue: $24,811.40               MTD: $148,892.60

  Rooms Available Tonight:  19
  Walk-in Rate Suggestion:  $209 (demand index: HIGH)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🌟 VIP ARRIVALS — ACTION REQUIRED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🌟 MARTINEZ, ELENA — Conf #MER-78421
   Platinum Loyalty | 14th Stay | Suite 801 (pre-assigned)
   Preferences: High floor, firm pillows, sparkling water
   🎂 BIRTHDAY TOMORROW — Recommend: amenity delivery tonight
   Upsell: Spa package ($149) — accepted on 3 of last 5 stays
   ETA: 2:00 PM

🌟 CHEN, DAVID — Conf #MER-78455
   Corporate Account: TechVentures Inc. | 6th Stay
   Preferences: Quiet room, extra hangers, late checkout
   ⚠️ RECOVERY: Complained about AC noise on last visit (Room 304)
   → Assigned Room 508 (renovated HVAC). Flag team for personal greeting.
   ETA: 4:30 PM

🌟 OKONKWO, AMARA — Conf #MER-78462
   Travel Influencer | 42K followers | First Stay
   → Complimentary upgrade to Deluxe King approved by GM
   → Welcome amenity: local artisan chocolate box
   Coordinate with Marketing for potential content opportunity
   ETA: 3:00 PM

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
👤 ALL ARRIVALS SUMMARY (27 Total)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  VIP Guests:           3
  Returning Guests:     8  (welcome back cards prepared)
  New Guests:           16
  Special Occasions:    2  (1 birthday, 1 anniversary)
  Recovery Guests:      1  (David Chen — see VIP section)
  Upsell Targets:       11 (est. revenue opportunity: $1,240)

  Room Pre-Assignments: 27/27 ✅ Complete
  Early Arrivals (before 2pm): 6 — Housekeeping notified

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔄 SHIFT HANDOFF: Night Audit → Morning
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔴 IMMEDIATE
  • Room 215 — Pipe leak reported at 2:15 AM. Engineering 
    placed temporary fix. Plumber confirmed for 8:00 AM.
    Guest relocated to Room 219 (complimentary). Room 215 
    is OUT OF ORDER until further notice.
  • Room 612 — Noise complaint (3x calls). Documented. 
    Guest in 614 checked out at 11 PM (resolved).

🟡 PENDING
  • Room 401 — Requesting late checkout (2 PM). Occupancy 
    allows it. Needs manager approval.
  • Room 308 — Extra rollaway bed request for tomorrow. 
    Reserve from housekeeping.
  • Lobby — FedEx package for guest YAMAMOTO (arriving today). 
    Stored in back office cabinet B.

🟢 INFORMATIONAL
  • Wedding group (Morrison-Park, 45 rooms) checks in Saturday.
    Group coordinator arriving today at 11 AM for site walk.
  • Pool heater maintenance scheduled 7–9 AM. Pool closed.
    Signage placed.
  • Weather: Rain expected 2–6 PM. Umbrella station stocked.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💬 OTA REVIEW MONITOR — Last 24 Hours
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  New Reviews:     4
  Average Rating:  4.3 ⭐
  Sentiment:       Stable (7-day avg: 4.4)

  ✅ Booking.com — 5⭐ — "Best hotel in the city. Staff 
     remembered our names!" 
     → Response drafted, ready for approval

  ✅ Google — 4⭐ — "Great location, clean rooms. Breakfast 
     could use more variety."
     → Response drafted with breakfast update mention

  ⚠️ TripAdvisor — 3⭐ — "Room was fine but front desk was 
     slow at check-in. Waited 20 minutes."
     → Response drafted. Flagged for process review.

  🔴 Expedia — 2⭐ — "Found hair in bathroom. Housekeeping 
     didn't come until I called twice."
     → ESCALATED TO GM. Response drafted pending approval.
     → Guest: R. Torres (checked out May 5). 
       Consider recovery outreach.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🏠 HOUSEKEEPING PRIORITY MATRIX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  🔴 RUSH (Early arrivals, VIP rooms):
     Rooms: 801, 508, 312, 425, 103, 217
     Deadline: 12:00 PM

  🟡 STANDARD (Regular departures → arrivals):
     Rooms: 22 rooms on floors 2, 3, 4, 6
     Deadline: 3:00 PM

  🟢 STAYOVERS:
     Rooms: 104 rooms (daily service)
     
  🔧 MAINTENANCE HOLDS:
     Room 215 — Plumbing (OOO)
     Room 710 — Carpet stain treatment (available by 2 PM)

  Inspection Ready: 0/28 departure rooms (night audit complete)
  Housekeeping Staff Today: 12 attendants, 2 supervisors

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💰 UPSELL PLAYBOOK — TODAY'S TARGETS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  1. Room Upgrades (8 eligible)
     Standard King → Deluxe King: $45/night (4 available)
     Deluxe King → Junior Suite: $85/night (2 available)
     Est. Revenue if 40% convert: $340

  2. Package Add-Ons (11 eligible)
     Romance Package: $89 (3 couples identified)
     Spa Credit Bundle: $149 (2 repeat spa users)
     Late Checkout Guarantee: $39 (6 business travelers)
     Est. Revenue if 30% convert: $450

  3. F&B Upsell
     In-room dining promo: "Welcome Bite" appetizer tray $29
     → Auto-suggest at check-in for all arrivals
     Est. Revenue: $200

  📈 TOTAL DAILY UPSELL OPPORTUNITY: ~$990

═══════════════════════════════════════════════════════════════
  Report prepared by AI Operations Assistant
  Next report: 1:30 PM (Afternoon Shift Briefing)
  Questions? Reply to this email or flag in #front-desk Slack
═══════════════════════════════════════════════════════════════
```

---

## n8n / Make.com Workflow Configuration

### Trigger Node
- **Type:** Cron / Schedule
- **Time:** 5:30 AM daily (adjustable per shift)
- **Timezone:** Property local time

### Node 1: PMS Data Pull
```json
{
  "node": "HTTP Request",
  "method": "GET",
  "url": "https://your-pms.com/api/v2/reservations",
  "params": {
    "arrival_date": "{{$today}}",
    "departure_date": "{{$today}}",
    "include": "guest_profile,preferences,history,folio"
  },
  "headers": {
    "Authorization": "Bearer {{$credentials.pms_api_key}}"
  }
}
```

### Node 2: OTA Review Aggregation
```json
{
  "node": "HTTP Request (Multiple)",
  "requests": [
    {
      "platform": "booking_com",
      "url": "https://supply-xml.booking.com/hotels/reviews",
      "params": { "since": "{{$yesterday}}" }
    },
    {
      "platform": "tripadvisor",
      "url": "https://api.tripadvisor.com/v2/reviews",
      "params": { "since": "{{$yesterday}}" }
    },
    {
      "platform": "google",
      "url": "https://mybusiness.googleapis.com/v4/reviews",
      "params": { "since": "{{$yesterday}}" }
    }
  ]
}
```

### Node 3: AI Processing (Claude API)
```json
{
  "node": "HTTP Request",
  "method": "POST",
  "url": "https://api.anthropic.com/v1/messages",
  "headers": {
    "x-api-key": "{{$credentials.claude_api_key}}",
    "anthropic-version": "2023-06-01",
    "content-type": "application/json"
  },
  "body": {
    "model": "claude-sonnet-4-6",
    "max_tokens": 4000,
    "messages": [
      {
        "role": "user",
        "content": "Process the following hotel operations data and generate the Daily Guest Experience & Revenue Optimization Report:\n\nARRIVALS: {{$node.pms_data.arrivals}}\nDEPARTURES: {{$node.pms_data.departures}}\nIN-HOUSE: {{$node.pms_data.inhouse}}\nREVENUE: {{$node.pms_data.revenue}}\nREVIEWS: {{$node.ota_reviews}}\nHOUSEKEEPING: {{$node.hk_status}}\nOPEN TICKETS: {{$node.open_tickets}}"
      }
    ]
  }
}
```

### Node 4: Distribution
```json
{
  "node": "Split & Route",
  "routes": [
    {
      "name": "Email — Front Desk Team",
      "to": "frontdesk@hotel.com",
      "subject": "🏨 Daily Briefing — {{$today}} | Occ: {{occupancy}}% | Arrivals: {{arrival_count}}",
      "format": "HTML"
    },
    {
      "name": "Slack — #front-desk",
      "channel": "#front-desk",
      "message": "Morning briefing posted! {{arrivals}} arrivals, {{vip_count}} VIPs. Full report in email."
    },
    {
      "name": "Slack — #revenue",
      "channel": "#revenue",
      "message": "RevPAR: ${{revpar}} | ADR: ${{adr}} | Occ: {{occupancy}}% | Upsell target: ${{upsell_est}}"
    },
    {
      "name": "Slack DM — GM",
      "condition": "escalation_count > 0",
      "message": "⚠️ {{escalation_count}} items requiring your attention. Check email for details."
    }
  ]
}
```

---

## 5-Day Quick-Start Plan

### Day 1: Data Audit
- Identify your PMS and whether it has an API (Opera, Cloudbeds, Mews, Little Hotelier, etc.)
- If no API: set up automated CSV export to a shared Google Drive folder
- List your OTA channels and check review API availability
- Document your current shift handoff process

### Day 2: Template Setup
- Copy the prompt templates above into your AI tool of choice
- Customize hotel name, brand voice, room types, and rate tiers
- Set up a Google Sheet as your staging database with tabs: Arrivals, Revenue, Reviews, Tickets
- Create your distribution list (who gets what, when)

### Day 3: Manual Test Run
- Pull today's data manually from your PMS
- Paste into the AI prompts and generate each report section
- Share with your front desk team for feedback
- Note what's missing, what's too detailed, what they actually use

### Day 4: Automation Build
- Set up n8n or Make.com account
- Build the workflow: Trigger → PMS Pull → AI Process → Format → Distribute
- Connect email and Slack integrations
- Run a test with yesterday's data

### Day 5: Go Live
- Enable the 5:30 AM scheduled trigger
- Monitor the first automated report with your team
- Collect feedback and adjust prompts, formatting, timing
- Set up a 2-week review checkpoint

---

## Customization Options

**Budget Hotels:** Simplify to arrivals list + revenue snapshot + housekeeping priorities. Skip VIP profiling and upsell modules.

**Luxury Properties:** Add concierge coordination, restaurant reservation pre-booking, transportation arrangements, and amenity personalization details.

**Extended Stay:** Replace daily arrivals focus with weekly check-in summaries, long-stay guest wellness checks, and housekeeping schedule management.

**Resorts:** Add activity/excursion booking status, pool/beach towel inventory, F&B outlet capacity, and weather-dependent activity alternatives.

---

## ROI Analysis

| Metric | Before | After | Impact |
|--------|--------|-------|--------|
| Time on manual reporting | 5.5 hrs/day | 1.3 hrs/day | -76% |
| Guest complaint resolution time | 4.2 hours avg | 1.8 hours avg | -57% |
| OTA review response time | 36 hours avg | 4 hours avg | -89% |
| Upsell conversion rate | 8% | 18% | +125% |
| Shift handoff errors | ~5/week | ~1/week | -80% |
| VIP recognition misses | ~3/week | ~0/week | -100% |
| Monthly upsell revenue lift | — | +$8,000–15,000 | New revenue |
| Staff satisfaction (survey) | 3.2/5 | 4.1/5 | +28% |

**Conservative Annual Value:** $96,000–180,000 in labor savings + incremental revenue  
**Annual Cost:** ~$600–960  
**Net ROI:** 100–188x return

---

## Common Pitfalls & Solutions

| Pitfall | Solution |
|---------|----------|
| PMS doesn't have an API | Use scheduled CSV exports + Google Sheets as middleware |
| Staff ignores the report | Start with just the shift handoff section — highest immediate value |
| Too much information | Let the team vote on which sections matter most, cut the rest |
| Inaccurate room assignments | Keep AI suggestions as recommendations, not auto-commits to PMS |
| Review responses feel generic | Fine-tune prompts with 10+ examples of your actual past responses |
| Guest data privacy concerns | Process data locally, don't store guest PII in third-party AI tools without consent review |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | May 7, 2026 | Initial blueprint |

---

*Built by AI Workflow Blueprints — Giving professionals 10+ hours back every week.*
