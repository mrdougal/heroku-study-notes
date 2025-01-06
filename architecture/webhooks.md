# Webhooks

`heroku webhooks:add` to subscribe
`heroku webhooks` to view subscriptions
`heroku webhooks:remove` to delete webhook

---

Send via `HTTPS POST` to an endpoint of your choosing
Respond with status `200` to confirm received notification. Heroku ignores the body, so `204` (no content) is considered ideal.

- Anything other that 200 series response is considered a failure

## Events you can subscribe to (per entity)

Every "event" has a "type" `create`, `destroy` or `update` - you receive notifications for all event types

- `-t` `--authorization` Can include custom headers for auth
- `-s` `--secret` value used to sign request. in `Heroku-Webhook-Hmac-SHA256` header.
  - On generation value is viewable only once.
  - Have to update value if need new one `heroku webhooks:update`
  - Your application should verify this value

## Securing requests

The secret from webhook creation used with request header to prove the secret signed the request.

If authorization flag can be used. Passed in the `Authorization` header. (This is the value provided on webhook creation)

## Viewing queue

`heroku webhook:deliveries`

Jobs have 4 possible status

- `pending` in the queue
- `success` sent and received a 200 response from endpoint
- `failure` all attempts in past 72 hours have failed
- `skipped` if more that 72 hours old then delivery will be attempted. (this appears if endpoint has been failing)

## Retries

Will attempt retries for 72 hours, after this grace period Heroku will discard the webhook
(You will be emailed of the removal of the webhook)

- All other notifications will be halted during retry process.
  - So you could end up with a large backlog of failed jobs
    - If a large backlog grows due to the failures, the webhook might be discarded

Can lookup past events via the `id` (if you have it) `heroku webhooks:deliveries:info DELIVERY_ID`
