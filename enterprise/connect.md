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

## Don't have to be enterprise but...

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
