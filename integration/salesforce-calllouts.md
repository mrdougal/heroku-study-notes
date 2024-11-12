# Callouts with Apex triggers

To perform on salesforce objects

- `insert`
- `update`
- `delete`
- `merge`
- `upsert`
- `undelete`

- Define callout to external system
- record triggered flow

## Setup

- Create "Outbound message" action.
- Set endpoint url to heroku app
- If `Send session id` is selected, token can be used to make REST API calls on users behalf
- Message sent as SOAP

## Use case
