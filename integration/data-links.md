# Heroku "data links" _foreign data wrappers in PG_

https://devcenter.heroku.com/articles/heroku-data-links

Data links handles the tricky part of foreign data wrappers.

- creating server spec
- create tables to query against


In practice PG queries the foreign db to get the results needed


## CLI

`heroku pg:links` 


## Gotcha's

Only the tables that existed at time of creating the data link will be visible. 
Need to recreate the data link, if need to expose newer tables.

Deleting or moving the remote data store, doesn't update the data link. 
Resetting the db via `heroku pg:reset` will also reset the foreign data source

Removing a data link. There are no checks if a link is being used (eg: in a view) before it is deleted.

No custom types in foreign data source. (if possible [create custom types](https://www.postgresql.org/docs/current/sql-createtype.html) in local db first)

Data links **not supported on Shield** plans. (even if foreign db is in same shield space)