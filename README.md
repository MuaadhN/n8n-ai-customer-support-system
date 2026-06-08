# n8n AI Customer Support & Digital Product Delivery System

A complete end-to-end automation system built with **n8n**, **OpenAI**, **WooCommerce**, and **Google Sheets** that handles digital product delivery and AI-powered customer support — fully automated with human approval before sending replies.

---

## What This System Does

| Feature | Description |
|---|---|
| Digital product delivery | Sends license keys automatically when an order is paid |
| AI support replies | Reads customer emails, looks up their order, and drafts a reply using GPT |
| Human approval gate | Admin reviews and approves every AI reply before it is sent |
| Order lookup | Verifies customer identity and order details via WooCommerce API |
| Stock management | Tracks license key inventory in Google Sheets, alerts admin when stock is low |
| Full audit log | Every delivery and support reply is logged to Google Sheets |

---

## System Architecture

```
WooCommerce Store
      │
      ├── Order Paid ──────────────► Workflow 1: Digital Product Delivery
      │                                    └─► Send license key via SMTP
      │
      └── Customer Emails (IMAP) ──► Workflow 2: Support Email Intake
                                           │
                                           ├─► Workflow 3: AI Support Agent
                                           │        ├─► Lookup order (WooCommerce)
                                           │        └─► Generate reply (OpenAI GPT-4o)
                                           │
                                           └─► Workflow 4: Human Approval Gate
                                                    └─► Workflow 5: Send Reply (SMTP)
```

---

## Workflows

### Workflow 1 — Digital Product Delivery
**Trigger:** WooCommerce webhook fires on order paid

```
[WooCommerce Webhook]
        ↓
[IF - Order Paid?]
        ↓ YES
[Code - Parse Order Data]
        ↓
[Google Sheets - Read License Keys]
        ↓
[Code - Find Available Key for Product]
        ↓
[IF - Key Available?]
   YES ─────────────────────────────── NO
    ↓                                   ↓
[Sheets - Mark Key as Used]    [Email Admin: Out of Stock]
    ↓
[Code - Build Delivery Email]
    ↓
[Send Email - Deliver Product]
    ↓
[Sheets - Log Order Delivery]
```

### Workflow 2 — Support Email Intake *(coming soon)*
Watches the support inbox via IMAP, classifies incoming emails by intent, and triggers the AI agent.

### Workflow 3 — AI Support Agent *(coming soon)*
Uses WooCommerce API to look up the customer's order, then passes all context to an OpenAI GPT-4o agent to draft a personalized reply.

### Workflow 4 — Human Approval Gate *(coming soon)*
Sends the AI draft to the admin with Approve / Reject links. Waits for approval before proceeding.

### Workflow 5 — Send Final Reply *(coming soon)*
Sends the approved email to the customer via SMTP and logs the interaction.

---

## Tech Stack

| Tool | Role |
|---|---|
| [n8n](https://n8n.io) | Automation engine (self-hosted or cloud) |
| OpenAI GPT-4o | AI email drafting and classification |
| WooCommerce REST API | Order lookup and customer verification |
| Google Sheets | License key inventory and audit logging |
| SMTP (Titan Mail / Gmail) | Sending delivery and support emails |
| IMAP | Reading incoming support emails |

---

## Google Sheets Structure

**Tab 1 — License Keys**
| product_id | product_name | license_key | status | assigned_to | assigned_date |
|---|---|---|---|---|---|
| 101 | Pro Software v2 | XXXX-YYYY-ZZZZ | available | — | — |

**Tab 2 — Order Log**
| order_id | customer_email | product_name | license_key | delivery_sent | sent_at |
|---|---|---|---|---|---|

**Tab 3 — Support Tickets** *(used by Workflows 2–5)*
| ticket_id | customer_email | subject | intent | ai_draft | status | replied_at |
|---|---|---|---|---|---|---|

---

## Setup

### Prerequisites
- n8n instance (cloud or self-hosted)
- WooCommerce store with REST API enabled
- Google account (for Sheets OAuth)
- SMTP email account (Titan Mail or Gmail)
- OpenAI API key

### Steps
1. Import the workflow JSON files into n8n
2. Create credentials in n8n:
   - **Google Sheets OAuth2** — connect your Google account
   - **SMTP** — your email host settings
   - **OpenAI API** — your OpenAI API key
   - **WooCommerce API** — your store URL + consumer key/secret
3. Replace placeholder values in each workflow:
   - `REPLACE_WITH_YOUR_SPREADSHEET_ID` — your Google Sheets ID
   - `support@yourdomain.com` — your support email address
4. Add the WooCommerce webhook pointing to your n8n webhook URL
5. Activate the workflows

---

## File Structure

```
/
├── Workflow 1 - Digital Product Delivery.json
├── Workflow 2 - Support Email Intake.json          (coming soon)
├── Workflow 3 - AI Support Agent.json              (coming soon)
├── Workflow 4 - Human Approval Gate.json           (coming soon)
├── Workflow 5 - Send Final Reply.json              (coming soon)
└── README.md
```

---

## Built With

- [n8n](https://n8n.io) — workflow automation
- [OpenAI API](https://platform.openai.com) — GPT-4o for AI replies
- [WooCommerce REST API](https://woocommerce.github.io/woocommerce-rest-api-docs/)
- [Google Sheets API](https://developers.google.com/sheets/api)
