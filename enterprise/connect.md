# Heroku Connect

- Sync data from salesforce to Heroku PG
  - Can be bi-directional
- Only syncs object and table data
- Configure which objects to sync
  - sync as `read` or `read/write`
- Create apps in Heroku to modify data and send to your db of record
- Can trigger Salesforce workflows
  - And other Lightening Platform features
- Configure which objects to sync
  - Read/write
- One or more orgs can sync to same PG db
- New connections default to current major API version
- No need to call Salesforce API
- Can reference data from Salesforce

## Polling

## Syncing from Salesforce into PG

- 2 - 60 mins (These count towards API usage, so configure how you see fit)
- **Polling on demand** uses streaming API (which isn't reliable indicator of change!)

## Syncing from PG to Salesforce

https://devcenter.heroku.com/articles/mapping-configuration-options#heroku-postgres-polling

- Polls for updates every 2mins. (can't be configured)
- Doesn't poll for additional changes while writing to Salesforce
- Updates in chronological order
- On completion, 2min countdown begins

> In addition to the two-minute poll, your database attempts to detect new or updated records and notify Connect to initiate a poll using `pg_notify`. This notification happens at most every 10 seconds. These intervals aren’t configurable.

## Mapping

- Can be read only or read/write
- Mapping sync's all records
- Validation on config
  - not a "big object" or platform event

## Unique Identifier

Custom field with external ID to prevent duplicate record (salesforce uses to identify external records)

- updates from Salesforce into PG look matches on `sfid` or by unique id if `sfid` is NULL
- if there's no `sfid` or unique id match, connect creates new duplicate record

- treat external id's as an alias for a salesforce id.
- don't change the ids'
- use a unique generate to create id. eg `uuid_generate_v4()`

## Don't have to be enterprise but on the `demo` plan...

Has limitations

- Max `10,000` rows
- Min polling `10 mins`
- Trigger log is archived after 7days vs 31 days

## Gotcha's

- Doesn't work with connection pooling
- Can't be automatically added to review apps. (has to be done manually)
- Can't use foreign data wrappers
  - As connect needs shared functions stored in the public schema
- Is implemented with PG triggers, if you have your own triggers this may mess with connect
- Assignment rules aren't run by default when data pushed into Salesforce
- Is an app specific add-ons so can't share across apps.
- If delete the app, the connection is deleted and tables are dropped
  - So backup db
