# AI Lead Qualification & Follow-up System

A complete AI-powered lead management system built in n8n.
It captures leads from multiple channels, scores and qualifies
them using AI, nurtures cold leads automatically, and assigns
hot leads to the right sales rep without manual work.

---

## The Problem

Most businesses treat every lead the same. Sales teams waste
hours chasing cold leads while hot ones go uncontacted for
hours. This system fixes that by qualifying every lead the
moment it comes in and routing it based on its actual potential.

---

## Business Impact

- Hot leads contacted in under 3 minutes of submitting a form
- Sales team spends zero time manually scoring or sorting leads
- Cold and warm leads nurtured automatically until ready to buy
- Every lead logged, tracked, and assigned with full audit trail

---

## System Overview

This repo contains 5 independent workflows, each tackling a
different aspect of lead qualification and follow-up. They are
not connected to each other but together demonstrate the full
range of approaches to building a lead management system in n8n.

| Workflow | What It Does |
|---|---|
| Lead Generator + Email Automation | Captures leads, scores them, and automates email replies |
| Lead Nurturing System | Follows up cold and warm leads automatically with AI |
| HubSpot AI Lead Qualification | Qualifies leads inside HubSpot CRM using AI and routes them |
| AI Powered Multi-Channel Lead Response | Responds to leads from WhatsApp, email, and web forms |
| GoHighLevel Hot Lead Assignment | Polls for hot leads and assigns them to reps via round robin |

---

## Architecture

### Workflow 1 — Lead Generator + Email Automation

**Phase 1: Capture and Score**

Lead Form Webhook
↓
Calculate Lead Score
↓
Save to Google Sheets
↓
Is Hot Lead?
├── True → Send Hot Lead Email → Append row to Sheet
↓
Is Warm Lead?
├── True → Send Warm Lead Email → Append row to Sheet
└── False → Send Cold Lead Email → Append row to Sheet

**Phase 2: Email Reply Automation**

Gmail Reply Trigger
↓
Extract Email Data
↓
Lookup by Thread ID (Google Sheets)
↓
If lead found?
├── Yes → Merge & Validate Lead → Prepare AI Prompt
│         ↓
│         AI Agent (OpenAI Chat Model)
│         ↓
│         Parse AI Response
│         ↓
│         Send Auto-Reply → Mark Message as Read
└── No → No Operation

---

### Workflow 2 — Lead Nurturing System

Google Sheets Trigger (new row added)
↓
Wait (follow-up delay)
↓
AI Processing — Create Email & Tag (OpenAI)
↓
├── Send Email (Gmail)
├── Update Status (Google Sheets)
└── Notify Team (Slack)

---

### Workflow 3 — HubSpot AI Lead Qualification

Webhook
↓
Get Full Contact Details (HubSpot)
↓
Extract Lead Fields
↓
Validate Email Present
├── Missing → Stop
↓
AI Qualification Agent (OpenRouter + Structured Output Parser)
↓
Merge Qualification Result
↓
Is Hot Lead?
├── True → HubSpot Update Contact (Hot) → Notify Rep Email
└── False → HubSpot Update Contact (Nurture)

---

### Workflow 4 — AI Powered Multi-Channel Lead Response

Website Form Webhook → Normalise Form Lead ──┐
Twilio Trigger → Normalise WhatsApp Lead ────┼──▶ Assign Lead ID
Gmail Trigger → Normalise Email Lead ────────┘
↓
Generate Reply (OpenRouter Chat Model)
↓
Extract & Format AI Reply
↓
Route by Channel
├── WhatsApp → Send WhatsApp Reply (Twilio)
├── Email → Send Email Reply (Gmail)
└── Form → Send Form Lead Email Reply (Gmail)
↓
Log Lead to Google Sheets
↓
Is Hot Lead?
└── True → Slack Hot Lead Alert

---

### Workflow 5 — GoHighLevel Hot Lead Assignment & Notification

Poll For Hot Leads (Every 5 Min)
↓
Get All Normalised Leads (Google Sheets)
↓
Filter Ready For Assignment
↓
Process One Hot Lead At A Time (Loop)
↓
Assign Rep (Round Robin)
↓
GHL — Tag Hot Lead & Assign Owner
↓
├── Notify Rep — Email
├── Notify Rep — SMS
└── Notify Lead — Booking Invite SMS
↓
Update Lead — Assigned (Google Sheets)
↓
Batch Complete

---

## Demo

[Watch Demo Video 1](https://habtech.neetorecord.com/watch/9de71d594381212663c6)

[Watch Demo Video 2](https://habtech.neetorecord.com/watch/9677cec84d44a9c4ff99)

---

## Stack

| Layer | Tool |
|---|---|
| Automation | n8n (self-hosted) |
| AI/LLM | OpenAI · OpenRouter |
| CRM | HubSpot · GoHighLevel |
| Communication | Gmail · Twilio (WhatsApp + SMS) · Slack |
| Data | Google Sheets |

---

## Setup Instructions

1. Import all 5 workflow JSON files into your n8n instance
2. Add credentials:
   - OpenAI API Key
   - OpenRouter API Key
   - HubSpot API Key
   - GoHighLevel API Key
   - Twilio Account SID + Auth Token
   - Gmail OAuth
   - Google Sheets OAuth
   - Slack Bot Token
3. Update Google Sheets IDs in all workflows
4. Set your lead scoring logic in Workflow 1 Calculate Lead Score node
5. Configure round robin rep list in Workflow 5 Assign Rep node
6. Activate all 5 workflows in order

---

## Files

| File | Description |
|---|---|
| `workflow-1-lead-generator-email-automation.json` | Lead capture, scoring, and email reply automation |
| `workflow-2-lead-nurturing-system.json` | AI-powered follow-up nurturing |
| `workflow-3-hubspot-ai-lead-qualification.json` | HubSpot CRM qualification and routing |
| `workflow-4-multichannel-lead-response.json` | Multi-channel AI response system |
| `workflow-5-ghl-hot-lead-assignment.json` | GoHighLevel hot lead assignment and notification |
| `assets/workflow-1.png` | Workflow 1 screenshot |
| `assets/workflow-2.png` | Workflow 2 screenshot |
| `assets/workflow-3.png` | Workflow 3 screenshot |
| `assets/workflow-4.png` | Workflow 4 screenshot |
| `assets/workflow-5.png` | Workflow 5 screenshot |

---

Built by [Sulyman Habeebullah](https://github.com/habtech-automation) — AI Automation Engineer
