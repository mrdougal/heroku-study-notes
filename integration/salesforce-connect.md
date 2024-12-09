# Salesforce connect

Integrate external data sources into Salesforce and between Salesforce orgs. **(Data isn't copied)**

Useful for backing a UI, or accessing data from external systems like SAP, Oracle etc.
Data is retrieved "in real time"

- [OData 2.0](./odata.md)
- REST with JSON or XML
- SOAP
- Heroku connect expose a Heroku PG db
- Heroku app can provide REST endpoints to be consumed by Salesforce connect
- limit of `20,000` OData callouts per hour (1,000 for dev edition)
  if you're hitting this limit need to switch to `Bulk API`, `SOAP API` or `"TreeSave API"` (can't find information on TreeSave though)
- can "composite batch" update, and perform (up to 25 sub-requests) with one request

- Can define relationships between datasets
- Data paging
- Search
- Can bring up to `100` tables from single source
- No limit on number of connections but are licenses needed for each source (can get expensive)

  - if getting too expensive could try [heroku connect](./heroku-connect.md), requires more work up front.

- Respects user permissions (with regards to what they can see)

## With Heroku

- Can use Heroku connect to expose data to Salesforce connect
- Any OData 2.0 source can be pulled into Salesforce
