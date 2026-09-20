# Flame Street Eats — AI Phone Ordering Receptionist

An AI voice receptionist that answers phone orders for a fast-food restaurant end-to-end: takes the order conversationally, confirms it, and automatically routes it into a database with a staff notification — no human order-taker required.

> Built as a portfolio project for an AI automation agency. Demonstrates a full voice-AI-to-automation pipeline: phone call → structured data → database → notification.

## Demo

[Watch the demo video](https://youtu.be/GO_qvOrM5jM)) — a full call, from order to database record to email notification, recorded live.

## The Problem

Restaurants lose orders and revenue when phone lines are busy during rush hours, and manual order-taking introduces delays and mistakes when staff relay orders to the kitchen by hand. This system answers every call instantly, takes the order accurately, and gets it into the kitchen queue with zero manual re-entry.

## What It Does

- Answers incoming calls and greets the customer
- Takes a full order conversationally — handles menu questions, customizations, quantities
- Distinguishes delivery vs. pickup, and collects the right details for each
- Reads the complete order back to the customer for confirmation before finalizing
- Automatically writes the confirmed order into a database (Airtable)
- Sends an instant email notification to restaurant staff when a new order arrives
- Handles real edge cases: off-menu requests, mid-order corrections, incomplete addresses

## Architecture

Caller phones in
↓
Vapi (voice AI — conversation, order-building, confirmation)
↓ (webhook, on order confirmation)
n8n (automation/orchestration)
↓
Edit Fields (renames data to match Airtable's schema)
↓
Airtable (order record created — the kitchen's order queue)
↓
Send Email (staff notified of the new order)


## Tech Stack

| Layer | Tool | Purpose |
|---|---|---|
| Voice AI | [Vapi](https://vapi.ai) | Handles the live phone conversation and order-taking logic |
| Orchestration | [n8n](https://n8n.io) (self-hosted) | Receives webhook data, transforms it, routes it |
| Database | [Airtable](https://airtable.com) | System of record for incoming orders |
| Notifications | Gmail SMTP | Staff email alert on new order |

## Key Design Decisions

- **Industry choice:** Fast-food ordering was chosen over an initial law-firm concept — fewer liability concerns, a more universally relatable demo, and a better fit for a pure inbound-conversation use case.
- **Notification pivot:** The original plan sent an SMS to the customer when their order was ready. Twilio's free trial is unavailable in the builder's region, so the notification was redesigned to alert restaurant staff by email the moment a new order comes in — a different but equally real feature.
- **Self-hosted automation:** n8n runs locally via Docker, tunneled publicly with ngrok, to keep the project cost-free during development.

## Known Limitations

This is a portfolio-ready demo, not a production deployment. Specifically:

- Runs on a local machine + free ngrok tunnel — not always-on. A real deployment would need a small always-on server (~$4-6/month).
- No live phone number yet — tested via Vapi's in-browser calling feature rather than a real dial-in line.
- Occasional speech-recognition inaccuracy on fast or heavily accented speech, a general limitation of current voice AI.
- Notifies staff rather than the customer directly (see notification pivot above); customer-facing SMS/email could be added with a working provider.

## Repository Contents

- `/workflow-export/` — exported n8n workflow (JSON)
- `/system-prompt/` — the full Vapi system prompt used by the assistant
- `/docs/screenshots/` — screenshots of the working system across all platforms

## Testing Performed

- Full multi-item orders (delivery and pickup)
- Off-menu item requests (correctly rejected, no invented items)
- Mid-order corrections (final order correctly reflects changes)
- Incomplete address handling (correctly asks for clarification)
