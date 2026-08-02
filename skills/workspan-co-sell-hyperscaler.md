---
name: Run a co-sell motion with a hyperscaler partner
description: Validate a CRM opportunity, find AWS partners, and fire co-sell actions (e.g. invite an AWS partner
  to an opportunity) through the Co-Sell API.
api: openapi/workspan-co-sell-openapi.yml
operations:
- post-token
- cosell-opportunity-crm-validate
- co-sell-list-aws-partner
- co-sell-count-aws-partner
- co-sell-picklist
- co-sell-action
---

## When to use
You are automating co-sell referrals between your CRM and a hyperscaler (AWS/Microsoft/Google) via WorkSpan.

## Steps
1. **Authenticate** — `post-token` (OAuth 2.0 client credentials) + `Ocp-Apim-Subscription-Key` header.
2. **Validate** — `cosell-opportunity-crm-validate` (`POST /cosell/opportunity/validate/crm`) with
   `crm_type` (e.g. `salesforce`) and the opportunity payload (customer, contacts, AWS fields such as
   `aws_use_case`, `aws_delivery_model`, `aws_partner_primary_need`).
3. **Pick values** — `co-sell-picklist` (`GET /metadata/picklists/hyperscaler`) for the allowed
   hyperscaler picklist values; never guess enum values.
4. **Find partners** — `co-sell-list-aws-partner` / `co-sell-count-aws-partner`.
5. **Act** — `co-sell-action` (`POST /cosell/action`) with an action such as `invite_aws_partner`
   (`inviting_from`, `opportunity_project_id` — WS-prefixed, `referral_id`, `partner_project_id`,
   `partner_responsibility[]`, `invitation_message`, `version: v1`).

## Rules
- `co-sell-action` documents 409 (conflict) and 429 (rate limit) — back off on 429; on 409 re-read
  state before retrying (no idempotency keys exist).
- Opportunity/partner project ids are WS-prefixed (`data-model/workspan-data-model.yml`).
