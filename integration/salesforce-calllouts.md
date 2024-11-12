# Callouts with Apex triggers

https://devcenter.heroku.com/articles/make-apex-and-workflow-callouts-to-your-api

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

- For processing out of Salesforce

## Gotcha's

- Async callouts are only through a **"Visualforce page"** v30 or later. (if has Javascript remoting it's `v31.0`)
- No support over "Private Connect"
