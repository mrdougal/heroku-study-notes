# Postgres

## CLI

- `Heroku pg:info`
- `heroku pg:psql`: drop into shell, needs postgres to be installed locally
- `heroku pg:pull` pull data onto local machine
  - Uses `pg_dump` and `pg_restore`
- `heroku pg:push` push from local to app
- `heroku pg:promote` promote db to primary for app.
  - Updates DATABASE_URL
    - Triggers restart and release scripts
  - Old db assigned `HEROKU_POSTGRESQL_<colour>_URL`

## Web dashboard

## Plans

### Essential

- 4hrs downtime a month 99.5% uptime
- No “fork and follow” support. (For db replication)
  - Fork for inspecting data, testing migrations etc
- No “expensive queries” support
- Unannounced maintenance and upgrades
- No logs

### Standard

- 1 hour of downtime
- No row limits
- Rollback up to 4 days
- Fork and follow
- DB metrics in logs
- Credential management
- Premium
  - 15min downiest
  - More memory
  - Plus all of standard

### Enterprise…

- Private
- Shield
  - For compliance

Discontinued plans
Can still use but will be migrated to “essential-0” and “essential-1”

- Mini
- Basic

All plans have...

- Health checks
- Write ahead log - every 60s
- Daily “logical backups”
- Dataclips
- SSL
- Extensions
- Encrypted at rest

Dataclips Share queries
Sharing DB between applications
Via “heroku addons:attached”
