---
name: Subscribe to WorkSpan events via webhooks
description: Discover available WorkSpan event types and register, inspect, and remove webhook subscribers with
  the Events API.
api: openapi/workspan-events-openapi.yml
operations:
- post-token
- GetEventList
- GetIntegrationList
- GetEventSchema
- WebhookRegistration
- ListSubscribers
- ReadSubscriber
- DeleteSubscriber
---

## When to use
You want WorkSpan to push object/event changes to your endpoint instead of polling.

## Steps
1. **Authenticate** — `post-token` (OAuth 2.0 client credentials) + `Ocp-Apim-Subscription-Key` header.
2. **Discover** — `GetEventList` (`GET /metadata/event`) for available events; `GetEventSchema` /
   `GetObjectEventSchema` for payload shapes; `GetIntegrationList` for the integrations you may bind to.
3. **Register** — `WebhookRegistration` to create a subscriber. A subscriber carries `name`,
   `objectids[]`, `integration_id`, `filters`, and `subscriber_type: webhook`.
4. **Verify** — `ListSubscribers` / `ReadSubscriber` (`GET /subscriber/{subscriber_id}`).
5. **Clean up** — `DeleteSubscriber` to unregister the webhook.

## Rules
- Handle 400 (`Invalid ID supplied`) and 404 (`subscriber not found`) responses explicitly.
- The concrete event catalog is account-specific — always call `GetEventList` rather than assuming
  event names (see `asyncapi/workspan-webhooks.yml`).
