# External objects

https://devcenter.heroku.com/articles/heroku-external-objects

- Part of connect. 
- Provides an [oData](./odata.md) wrapper for PG database configured for use with connect. (Creates RESTful endpoints)
- Used with Salesforce connect can be consumed from Apex, Visualforce pages. (viewed, searched and linked to)

- Can't be used in reports, as the data isn't copied. 


## Auth

Used basic auth.
Credentials can be managed via "External objects settings" page

- New and old credentials are accepted in a 10min window after a credential change

## Rate limits

- `20,000` requests per hour for `shield`, `enterprise` or `danketsu` connect plans 
- for demo plan it's `1,000` requests an hour
- Client will get `429 Too Many Requests` error


## Logging

All goes into logdrain

- Credential resets 
- Config change
- Hitting rate-limit


## Data-types

If the type isn't supported the field isn't in the results

- `bigint`
- `boolean`
- `bytea`
- `character` _character varying_
- `date`
- `double precision`
- `integer`
- `numeric`
- `real`
- `smallint`
- `text`
- `time` _time of day isn't supported_
- `timestamp`
- `uuid`

## Gotcha's

- Case insensitive search isn't supported
- Batch Apex isn't able to query Heroku external objects
- "External change data capture" isn't supported (track changes to data outside of Salesforce org)
- "Free-Text search expression" isn't supported https://help.salesforce.com/s/articleView?id=platform.odata_query_string_options.htm&type=5
- In Global Search, results appear under "External results"
- "Compress requests" for an external data source in Salesforce isn't supported. (gzip requests)
- **20 sec time limit**, otherwise returns `530` response
- Calculating total records is expensive, so paginated results are limited to 50 results. (+1 to indicate there are more records)
- **Composite primary keys** are unsupported. (Workaround is to copy values into a column and set that column as the primary key)
- **Partitioned PG tables** are unsupported
