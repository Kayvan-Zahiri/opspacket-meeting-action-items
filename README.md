# OpsPacket: Meeting → Action Items (Teaser)

Turn a meeting transcript into structured action items and route them to Notion and/or Linear with n8n.

**This public repository is a teaser/docs/samples only. The complete workflow pack is paid and available on Gumroad.**

- Landing page: https://opspacket-meeting-actions.netlify.app/
- Patterns blog: https://opspacket-meeting-actions.netlify.app/blog/
  - [Meeting transcript → Notion with n8n](https://opspacket-meeting-actions.netlify.app/blog/meeting-to-notion-n8n.html)
  - [Notion action-item schema](https://opspacket-meeting-actions.netlify.app/blog/notion-action-items-schema.html)
  - [Why null due dates beat invented Fridays](https://opspacket-meeting-actions.netlify.app/blog/null-dates.html)
  - [Webhook contract](https://opspacket-meeting-actions.netlify.app/blog/webhook-contract.html)
- Full pack: https://kayvanandre.gumroad.com/l/smbqns

## The pattern

1. Accept a transcript through a manual test node or webhook.
2. Normalize the meeting title, destination, and transcript.
3. Send the transcript to an OpenAI-compatible LLM with a structured action-item schema.
4. Parse and validate the extracted actions (title, owner, due date, priority, notes).
5. Fan out to Notion database pages, Linear issues, or both.
6. Return a compact success response with created-item links.

The workflow is designed to preserve ambiguity: relative or incomplete dates can remain empty instead of being invented, and unassigned work remains unassigned.

## Setup overview

You need n8n (cloud or self-hosted), an OpenAI-compatible LLM endpoint, and credentials for the destinations you use.

- Import the purchased n8n workflow JSON.
- Configure the LLM HTTP credential and endpoint/model.
- For Notion, share a database with the integration and map Name, Owner, Due Date, Priority, and Meeting properties.
- For Linear, configure the team ID and API credential; owner names are documented rather than auto-assigned in the starter version.
- Test with the sample transcript, then activate the webhook only after credentials and access controls are configured.

The full setup guide and importable workflow are included with the Gumroad pack; this repository intentionally does not publish the paid workflow JSON.

## Samples

- [Sample input transcript](examples/SAMPLE_INPUT.md)
- [Sample output](examples/SAMPLE_OUTPUT.md)

## License / usage note

The paid OpsPacket pack is licensed for personal and commercial use of the workflow within your organization. It does not permit resale, redistribution, or republishing of the pack, its files, docs, or landing assets as a competing product. This repository contains only teaser documentation and illustrative samples.

## Topics

n8n · Notion · Linear · meeting automation · action items
