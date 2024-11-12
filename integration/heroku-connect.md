# Heroku connect

https://devcenter.heroku.com/articles/managing-heroku-connect-mappings

Bi-directional sync between Salesforce and Heroku

See [connect](../enterprise/connect.md)

## SOAP API

- Best for small number of records. (less than `10,000` records)
- reloading all data into existing mapping
- dealing with changes from Salesforce
- counting records
- query on mapped fields
- used when **writing to Salesforce**

> SOAP API calls don't count towards API request limits. (counted for your org though)

## Bulk API

- For loading large datasets
- Async
- reloading all data
- **reading changes from salesforce**
