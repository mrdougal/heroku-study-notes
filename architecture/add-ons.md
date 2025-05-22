# add-ons

Extend Heroku

- Data stores
- Logging
- Monitoring
- Content Management

## Side effects

Sets config vars
Reads or write to logs
Uses “Platform API” for scaling on behalf of devs

Have their own plans

Install via cli or marketplace

- On install a “resource” for the add-on is added. This is the association between the app and the add-on
  - Most likely this will trigger some resources. Eg: for datastore a new db will be created.
  - Returns with a url that your app uses to access the resource.
    - Eg: `MYSQL_URL=mysql://user:pass@mysqlhost.net/database`
  - Your app is then rebuilt and dynos restarted.

Communication typically HTTP. TCP or a db protocol. eg: PG, Redis
