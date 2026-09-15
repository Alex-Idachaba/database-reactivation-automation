# Database Reactivation Automation

**Pattern:** Routing + Cohort Segmentation

Win-back automation for dormant customers — scheduled detection, cohort-specific outreach, and automatic reply routing so no dormant contact requires manual attention to start moving again.

## Problem

Every business with a CRM has a graveyard of dormant contacts — people who bought or inquired once and went quiet — sitting there with no process to bring them back into an active sales or marketing motion. That's revenue the business already paid to acquire, going unworked.

## Build

A scheduled query flags dormant customers using a `NOT EXISTS` check against recent activity, running on a cadence without manual list-pulling. A Switch node segments dormant customers into cohorts by dormancy characteristics, and OpenAI drafts outreach personalized per cohort. Replies are classified into four categories automatically and routed accordingly.

One build detail worth documenting: campaign matching originally relied on timestamps, but a TIMESTAMPTZ precision loss introduced through n8n's JavaScript layer caused replies to mismatch against the campaign that generated them. The fix was switching to id-based campaign matching instead of timestamp comparison.

## Outcome

Dormant customers get systematic win-back outreach instead of being ignored indefinitely, and replies route themselves — hot leads land back in active follow-up without anyone monitoring for them.

## Reliability Notes

The TIMESTAMPTZ fix is a direct example of the idempotency/logging rungs in practice — the original timestamp-based matching was fragile under real timing conditions, and the id-based fix removed that fragility rather than patching around it.

## Stack

n8n, Postgres, OpenAI

## Status

Complete.
