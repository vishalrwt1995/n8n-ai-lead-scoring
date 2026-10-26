# AI lead scoring for n8n: webhook → AI score → Google Sheets → hot-lead alert

A free, import-ready n8n workflow. Point any website form at it, and every lead gets:

- **normalized:** Typeform, Tally, Webflow, WordPress CF7/WPForms, Framer or plain HTML field names all map to one schema
- **validated:** a bad email gets HTTP 400, and a honeypot field catches bots
- **scored 0–100 by an LLM** against *your* ideal customer, services and minimum budget, with need, urgency, budget band, intent, tags and reasoning as strict JSON
- **still scored if the AI is down**, by a rule-based fallback, so no lead is dropped
- **upserted into Google Sheets by email**, so there are no duplicate rows
- **tiered A / B / C / SPAM**, with an email to you for A-tier leads

Works with any OpenAI-compatible API (OpenAI, OpenRouter, Groq…) or **free local Ollama**. Core nodes only, no community nodes, no credentials in the file.

## Import
1. Download `workflows/lead-scoring-lite.json`.
2. In n8n: **Workflows → Import from file**.
3. Follow the yellow sticky note on the canvas (about 10 minutes).

## Google Sheet headers (row 1 of a `Leads` tab)
`lead_id, created_at, updated_at, source, name, email, phone, company, website, budget, service, message, need_summary, service_match, urgency, budget_band, intent, score, tier, tags, reasoning, scored_by, status`

## Test it
```bash
curl -X POST "$N8N_WEBHOOK_URL" -H 'Content-Type: application/json' \
  -d '{"name":"Jo Test","email":"jo@example.com","company":"Example Co","budget":"3000","message":"Need help with paid ads this month"}'
# → {"ok":true,"lead_id":"L-…","tier":"A"}
```

## How it was tested
Imported and run on n8n 2.44.6 with a mock LLM and 5 sample leads (hot, warm, cold, vendor spam, invalid email): all were routed correctly. A second run with the LLM unreachable fell back to rules, and all 4 valid leads were still logged.

## Example output (row written to the Sheet)
| email | score | tier | need_summary | scored_by |
|---|---|---|---|---|
| priya@brightcart.example | 88 | A | DTC brand needs paid ads takeover after ROAS drop | ai |
| tom.becker@northwind.example | 61 | B | B2B logistics wants CRO audit and landing page redesign | ai |
| sam@example.com | 22 | C | Asks website pricing | ai |
| alex@rankfast.example | 0 | SPAM | Vendor pitching SEO | ai |

*(Fictional test leads.)*

## Want the full pipeline?
This repo is the intake and scoring step. I also sell a paid 7-workflow version, **Lead Autopilot for n8n**. It adds an instant reply with your booking link, a 3-step follow-up sequence that stops itself on reply, IMAP reply detection, a daily 08:00 pipeline report, monthly re-engagement, error alerts, niche presets and a PDF guide: https://vishalrwt.gumroad.com/l/n8n-lead-autopilot

## License
MIT. Use it, change it, install it for clients.
