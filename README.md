# Event Registration & QR Pass Generator

An automated event registration system built with **n8n** that handles the entire registration process — from form submission to QR pass delivery — without any manual work.

---

## What It Does

When a student fills out a Google Form, the workflow automatically:

1. Detects the new form submission via Google Sheets
2. Generates a unique Registration ID (e.g. `EVT-2026-7832`)
3. Saves the registration data to a Google Sheet
4. Generates a personalized QR code via a free REST API
5. Sends a confirmation email with the QR pass to the student

All of this happens in under 2 minutes with zero manual effort.

---

## Problem It Solves

Manual event registration is slow and error-prone. Event coordinators typically have to:

- Manually assign registration IDs
- Update spreadsheets one by one
- Send confirmation emails individually

This automation eliminates all of that. One form submission triggers the entire pipeline automatically.

---

## Workflow Architecture

```
Google Form (student submits)
        ↓
Google Sheets Trigger (n8n detects new row)
        ↓
Code Node (generates unique Registration ID)
        ↓
Google Sheets Node (saves to Confirmed Registrations sheet)
        ↓
HTTP Request Node (calls free QR code API)
        ↓
Gmail Node (sends confirmation email with QR pass)
```

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| n8n | Workflow automation engine |
| Google Forms | Student registration form |
| Google Sheets | Data storage for responses and confirmed registrations |
| QR Server API | Free REST API to generate QR code images |
| Gmail | Sending confirmation emails |
| JavaScript | Custom code node for Registration ID generation |

**Cost: 100% free** — no paid APIs or services used.

---

## Sample Output

The student receives an email like this:

```
Subject: Registration Confirmed — EVT-2026-7832

Hello Rahul,

Your registration is confirmed!

Registration ID : EVT-2026-7832
Branch          : CSE
Year            : 3rd Year

[QR CODE IMAGE]

Show this QR at the entrance.
— Tech Fest 2026 Event Team
```

---

## Google Sheets Structure

**Sheet 1 — Form Responses 1** (auto-created by Google Forms)

| Timestamp | Name | Email | Branch | Year |
|-----------|------|-------|--------|------|
| 6/12/2026 10:46 | Rahul | rahul@gmail.com | CSE | 3rd Year |

**Sheet 2 — Confirmed Registrations** (written by n8n)

| Registration ID | Full Name | Email | Branch | Year |
|----------------|-----------|-------|--------|------|
| EVT-2026-7832 | Rahul | rahul@gmail.com | CSE | 3rd Year |

---

## Registration ID Logic

```javascript
// Code node in n8n
const name   = $json['Name'];
const email  = $json['Email'];
const branch = $json['Branch'];
const year   = $json['Year'];

const randomNum = Math.floor(Math.random() * 9000) + 1000;
const registrationId = `EVT-2026-${randomNum}`;

return [{ json: { name, email, branch, year, registrationId } }];
```

---

## QR Code API

Uses the free [QR Server API](https://goqr.me/api/) — no API key required.

```
GET https://api.qrserver.com/v1/create-qr-code/?size=200x200&data=EVT-2026-7832-Rahul
```

Returns a PNG image of the QR code which is embedded directly in the email.

---

## How to Run This Yourself

1. **Create Google Form** with fields: Name, Email, Branch, Year
2. **Link form to Google Sheets** (Responses → Link to Sheets)
3. **Create a second sheet** called `Confirmed Registrations` with columns: Registration ID, Full Name, Email, Branch, Year
4. **Import the workflow** — download `workflow.json` from this repo and import it into your n8n instance
5. **Set up credentials** — connect your Google account (OAuth2) in n8n
6. **Activate the workflow** — click Publish in n8n
7. **Test** — submit the form and check your inbox

---

## Project Setup Requirements

- n8n account (free trial at [n8n.io](https://n8n.io) or self-hosted with Docker)
- Google account (Gmail + Google Forms + Google Sheets)
- No other paid services required

---

## Skills Demonstrated

- Workflow automation with n8n
- REST API integration (QR Server API)
- Google Workspace integration (Forms, Sheets, Gmail)
- JavaScript for custom logic in automation
- HTML email templating
- End-to-end data pipeline design
- OAuth2 authentication setup

---

## Author

B.Tech 3rd Year Student  
Built as an internship portfolio project to demonstrate automation and API integration skills.
