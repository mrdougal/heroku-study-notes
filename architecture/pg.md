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

| Plan      | Amount of downtime per month | Fork | Follow | Rollback | HA  | Disk Encryption |
| --------- | ---------------------------- | ---- | ------ | -------- | --- | --------------- |
| Essential | up to 4 hours                | no   | no     | no       | no  | yes             |
| Standard  | up to 1 hour                 | yes  | yes    | 4 days   | no  | yes             |
| Premium   | up to 15min                  | yes  | yes    | 1 week   | yes | yes             |
| Private   | less than 15min              | yes  | yes    | 1 week   | yes | yes             |
| Shield    | less than 15min              | yes  | yes    | 1 week   | yes | yes             |

### Essential

- 4hrs downtime a month 99.5% uptime
- No “fork and follow” support. (For db replication)
  - Fork for inspecting data, testing migrations etc
- No “expensive queries” support
- Unannounced maintenance and upgrades
- No PG logs
- No additional credentials

### Standard

- 1 hour of downtime
- No row limits
- Rollback up to 4 days
- Fork and follow
- DB metrics in logs
- Credential management
- RAM 4Gb - 1Tb
- Disk 64Gb - 8Tb

## Premium

- 15min downtime
- Plus all of standard (ram and disc limits same)

### Enterprise…

- Private
- Shield
  - For compliance

## Discontinued plans

Can still use but will be migrated to “essential-0” and “essential-1”

- Mini
- Basic

All plans have...

- Health checks
- Write ahead log - every 60s
- Daily “logical backups” via PG Backup (not supported in shield)
- Dataclips (not supported in shield)
- SSL
- Extensions
- Encrypted at rest (except for hobby)

## Dataclips Share queries

- Via `json` `csv`
- Sharing DB between applications
- Via “heroku addons:attached”

### Charting with dataclips

- if 2+ columns
- for timeseries columns is a date or number
- for categorical data, columns string and number

- shared urls different from editing
  - csv, json urls
- can import into Google sheets via url

## Follower dbs'

| Leader               | Followers                      | Allowed |
| -------------------- | ------------------------------ | ------- |
| Common runtime       | Common runtime                 | yes     |
| Common runtime       | Private space                  | yes     |
| Private space        | Common runtime                 | yes 1   |
| Private space        | Private space same space       | yes     |
| Private space        | Private space different space  | yes 1   |
| Shield Private space | Shield Private same space      | yes     |
| Shield Private space | Shield Private different space | yes 2   |
| Shield Private space | Private space/Common runtime   | yes     |

1. Enable "Trusted IP ranges for data services" (to avoid follower lag)
2. Will see lag as "Trusted IP ranges for data services" isn't available in shield

Follower db needs to be able to accommodate data
Number of followers is limited
Lag depends on db updates, normal to be a few secs behind
Followers don't have to be same plan as leader (can be up to 4 plans below)
