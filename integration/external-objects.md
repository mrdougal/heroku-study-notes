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