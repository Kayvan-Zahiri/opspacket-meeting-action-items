# OpsPacket — Meeting → Action Items (teaser)

Turn a meeting transcript into structured action items in **Notion** and/or **Linear** using n8n + an OpenAI-compatible LLM.

This repo is a **public teaser**: sample I/O and setup notes. The importable workflow pack is sold separately.

## Links
- Landing: https://opspacket-meeting-actions.netlify.app/
- Buy the pack ($97): https://kayvanandre.gumroad.com/l/smbqns

## Pattern
1. Webhook or manual trigger receives `{ transcript, meeting_title, destination }`
2. LLM extracts JSON: `title`, `owner` (nullable), `due_date` (ISO or null), `priority`, `notes`
3. Switch on `destination`: `notion` | `linear` | `both`
4. Write Notion database pages and/or Linear issues

**Design rule:** prefer `null` owners/dates over invented ones.

## What’s in this repo
- `SETUP.md` — credentials and schema
- `SAMPLE_INPUT.md` / `SAMPLE_OUTPUT.md` — expected shapes

## What’s in the paid pack
- Importable `meeting-to-actions.n8n.json`
- Same docs, ready to import

## License
Samples and docs here: use freely for learning. Do **not** resell the paid workflow pack.

Not affiliated with n8n, Notion, or Linear.
