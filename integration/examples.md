https://trailhead.salesforce.com/content/learn/modules/connectors-for-data-integration/archive-and-consolidate-salesforce-data

## Consolidating multiple salesforce orgs

Sharing data can be difficult.

"Hub and spoke" and [Salesforce connect](./salesforce-connect.md). All data flows into hub and out to the spokes (or secondary orgs)
[Heroku connect](./heroku-connect.md) helps to sync multiple orgs into 1 PG database

## Consolidating siloed department org data

Customer needs to centralise data from...

- 2 sales orgs
- 1 service org
- Would also like to create a custom interface for each line of business.

### Solution

- Use [Heroku connect](./heroku-connect.md) to consolidate into one PG database. Any additional processing of data can happen on Heroku.
- Use [Salesforce Connect](./salesforce-connect.md) to then pull data from Heroku into main org using `OData` API
  - support of `OData 2`, `OData 4`, cross org
  - limit of `20,000` OData callouts per hour (1,000 for dev edition)
