# Heroku Connect

https://devcenter.heroku.com/categories/heroku-connect

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

On creation defaults to "current major release version of Salesforce API", can specify other versions on creation.

## Considerations

- Recommend decent connection limits and I/O performance. `standard-4` `premium-4` or `private-4` or `shield-4`
- Network latency can become an issue. Best to co-locate Connect and PG in same region as close to Salesforce Org
- After authenticating to an org, you can't change it late
- API version selected during setup can't be changed later

## Performance

Factors that effect performance

- mapping many columns on an object
- high volume of changes
- number of rows being synced
- using ORM that touches or saves non-existant changes
- db load, indexes

## Polling Salesforce to PG

## Syncing from Salesforce into PG

- 2 - 60 mins (These count towards API usage, so configure how you see fit)
- **Standard** Polls Salesforce for changes every 10mins. (can configure from `2 - 60 mins`)
- **Accelerated** or **Polling on demand** uses streaming api to notify of changes but…
  - streaming isn't reliable indicator of change
  - is available on custom object but not all standard objects
  - no information of what changed is sent

## Syncing from PG to Salesforce

https://devcenter.heroku.com/articles/mapping-configuration-options#heroku-postgres-polling

- Polls for updates every 2mins. (can't be configured)
- Doesn't poll for additional changes while writing to Salesforce
- Updates in chronological order
- On completion, 2min countdown begins

> In addition to the two-minute poll, your database attempts to detect new or updated records and notify Connect to initiate a poll using `pg_notify`. This notification happens at most every 10 seconds. These intervals aren’t configurable.

## SOAP Api

See [SOAP API](../integration/soap.md)

## Bulk API

For loading large datasets (more than `10,000` records)

- Async
- reloading all data
- **reading changes from salesforce**

Can be used for writing on `read/write` mappings

https://devcenter.heroku.com/articles/managing-heroku-connect-mappings

## Mapping

- Can be read only or read/write (defaults to read only from Salesforce)
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

## Algorithm for merging data

### "Ordered writes"

Default algorithm

- applies changes from the trigger log in order that they occurred
- can be slower to sync as each change is processed separately (even if on the same record)
- **a failed INSERT will stop the queue**. As all changes are processed in order.
- rapid switching between updates means connect will use the SOAP api. which means less can be packaged into each update. (more calls)

### "Merged writes"

- condenses and reorders changes from trigger log to maximise number of records in each SOAP message.
- better performance

But

- can mess up dependencies, especially if they're circular dependencies. (can make it impossible to reliably establish some relationship types)
- use with caution if use Apex triggers and process rules. (As they're not guaranteed to see all changes made to database)
- does attempt to resend failed inserts
- can't use the bulk api

## Gotcha's

- Doesn't work with connection pooling
- Can't be automatically added to review apps. (has to be done manually)
- Can't use foreign data wrappers
  - As connect needs shared functions stored in the public schema
- Is implemented with PG triggers, if you have your own triggers this may mess with connect
- Assignment rules aren't run by default when data pushed into Salesforce
- Is an app specific add-ons so can't share across apps.
- If delete the app, the connection is deleted and tables are dropped
  - So backup db first

- **Handling binary files** https://devcenter.heroku.com/articles/heroku-connect-database-tables#unsupported-data-types  In short they're not supported, as they're Base64 encoded. Connect doesn't support them as they're not supported in Bulk API queries. _(Alternatively copy the file to S3 and sync the URL)_

- **Compound fields** (eg: an address is made up of multiple fields). Not possible to be mapped directly, but can map the underlying fields like postcode, city etc


## Errors

If there's been an error due to Salesforce is not available (eg: due to service windows), writes are queued until available again

- Otherwise manual intervention is required
- Notifications sent to users chosen to receive them
  https://devcenter.heroku.com/articles/heroku-connect-logs-errors#errors-with-associated-notifications

View errors in `_trigger_log_` in pg

```sql
SELECT table_name, record_id, action, sf_message
  FROM salesforce._trigger_log
  WHERE state = 'FAILED';
```
