# Salesforce connect

Integrate external data sources into Salesforce and between Salesforce orgs. **(Data isn't copied)**
Useful for backing a UI, or accessing data from external systems like SAP, Oracle etc.

- OData 2.0
- REST with JSON or XML
- SOAP
- Heroku connect expose a Heroku PPG db
- Heroku app can provide REST endpoints to be consumed by Salesforce connect
- limit of `20,000` OData callouts per hour (1,000 for dev edition)

- Can define relationships between datasets
- Can bring up to `100` tables from single source
- No limit on number of connections but are licenses needed for each source (can get expensive)

  - if getting too expensive could try [heroku connect](./heroku-connect.md), requires more work up front.

- Respects user permissions (with regards to what they can see)
