# Ada - WhatsApp Real Estate AI Assistant (n8n)

An AI WhatsApp assistant for a real estate company. Ada answers questions, searches listings, sends photos and brochures, captures leads, books viewings, and hands over to a human team through Telegram.

**43 nodes** - built with [n8n](https://n8n.io),whatsapp, real-estate, ai-agent, chatbot, automation, gemini

## How it works

1. **WhatsApp Trigger** - receives client messages (text, voice notes, photos).
2. **Human handover check** - a Data Table tracks which clients are paused for a human; if so the message is forwarded to the team's Telegram instead of Ada.
3. **Message routing** - voice notes are transcribed with Gemini; photos are described with Gemini vision; other types get a polite reply.
4. **Ada agent** - Gemini (OpenRouter fallback) with memory per phone number and these tools: `search_listings`, `business_knowledge` (FAQ sheet), `save_lead`, `book_viewing`, `add_listing_for_owner`, `send_property_photo`, `send_brochure`, `notify_team`, `escalate_to_human`, `set_alert_preference`.
5. **Team commands** - in the team Telegram chat, `/pause <phone>` and `/resume <phone>` (or reply to a forwarded message) turn Ada off/on for a client.

## Credentials you need

- WhatsApp Business Cloud API (account + trigger OAuth)
- Telegram Bot API
- Google Sheets OAuth2
- Google Gemini (PaLM) API
- OpenRouter API (fallback model)

## Setup

1. In n8n, choose **Workflows -> Import from File** and select `workflow.json`.
2. Create/assign the credentials listed above on each node that asks for one.
3. Create a Google Sheet with tabs `Listings`, `FAQ`, `Leads`, `Bookings`. Suggested columns - Listings: `title, area, city, property_type, listing_type, price, bedrooms, description, image_urls, brochure_url, status, alerted`; Leads: `phone, name, requirements, budget, area, property_type, status, opted_in_alerts, last_message, last_contact_at, follow_up_sent, created_at`; Bookings: `phone, name, listing_title, viewing_datetime, status, reminder_sent`; FAQ: your own question/answer columns.
4. Replace `YOUR_GOOGLE_SHEET_ID` in the six Google Sheets tool nodes.
5. Create an n8n **Data Table** named `ada handover` with string columns `phone, status, reason, updated_at`, and select it in the data table nodes (replacing `YOUR_DATA_TABLE_ID`).
6. Replace `YOUR_WHATSAPP_PHONE_NUMBER_ID` in **typing indicator** and **WA Tell Client Ada Is Back** with your WhatsApp Business phone number ID.
7. Replace `YOUR_TELEGRAM_CHAT_ID` (team group chat) in the Telegram nodes and the **Team Command Trigger**.
8. In **WA Prepare Ada Input**, set `company_name` (replacing `YOUR_COMPANY_NAME`) and optionally `owner_agent_numbers` (phone numbers allowed to add listings).
9. Test with a manual run, then activate the workflow.

## Notes

- This export has been sanitized: credential IDs, chat IDs, sheet IDs, phone number IDs and webhook IDs were removed or replaced with `YOUR_...` placeholders.
- Never commit real API keys, tokens or customer data to this repo.

## License

MIT
