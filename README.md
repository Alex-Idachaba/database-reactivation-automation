# Database Reactivation Automation

**Pattern:** Routing + Cohort Segmentation · **Stack:** n8n, PostgreSQL, OpenAI · **Status:** Complete (tested with sample contacts)

Finds contacts who've gone quiet, sends them personalized outreach on a schedule, and sorts every reply automatically, so leads and customers the business already paid to acquire don't go to waste.

**Property management application:** lease renewal outreach before leases end, and re-engaging past property-owner inquiries that never signed.

---

## The problem

Every contact database has a pile of dormant contacts: people who inquired or bought once and went quiet. Without a process to bring them back, that's revenue the business already paid to acquire, going unworked.

## How it works

Scheduled dormancy query → cohort segmentation → cohort-specific outreach drafting → send → reply classification (four categories) → route to next step

- **Detection:** a scheduled query flags dormant contacts using a `NOT EXISTS` check against recent activity, with no manual list-pulling.
- **Segmentation:** a Switch node sorts dormant contacts into cohorts by their dormancy characteristics.
- **Outreach:** OpenAI drafts messages tailored to each cohort, instead of one generic "we miss you" email.
- **Reply handling:** replies are classified as interested, soft no, unsubscribe, or needs a person, and routed accordingly.

## Workflow structure

- **Stage 1:** Dormant Contact Detection & Segmentation (scheduled)
- **Stage 2:** Personalized Outreach Drafting & Sending

## Engineering note: timestamp precision loss

Campaign matching originally relied on timestamps. Testing revealed that TIMESTAMPTZ values lost precision passing through n8n's JavaScript layer, so replies failed to match the campaign that generated them. The fix was switching to ID-based campaign matching, which removed the fragility instead of patching around it.

## Reliability

| Rung | How it shows up |
|---|---|
| Idempotency | Campaigns are matched by stable IDs, not timestamps |
| Logging | Outreach and replies are tracked in PostgreSQL |
| Human fallback | Ambiguous replies are routed to a person instead of being auto-handled |

## Repository contents

- n8n workflow export (JSON)
- Workflow screenshots

## Running it yourself

1. Import the workflow JSON into n8n.
2. Create n8n credentials for PostgreSQL, OpenAI, and the email account used for outreach. Credentials are **not** included in the export.
3. Create the required PostgreSQL tables and load your contact data.
4. Set the dormancy threshold and cohort rules for your business.
5. Run the schedule manually once against test contacts before enabling it.

## Limitations

- Cohort rules are defined manually in the Switch node.
- Reply classification uses an LLM, so edge cases can be misclassified; the "needs a person" category exists to catch them.

---

Built by [Alex Idachaba](https://alexidachaba.com) — AI automation for property management operations.
