You can share a Heroku Redis instance between multiple applications.
You can populate a new Heroku Redis instance using another Redis instance, using `heroku addons:create`

```bash
heroku addons:create heroku-redis:premium-0 --fork rediss://h:<password>@<hostname>:<port> -a example-app
```

## Upgrading

- Run in place `heroku redis:upgrade`
- Create and promote new instance via `--fork`

## Failover

### Conditions

Heroku runs a suite of checks to ensure that a failover is the appropriate response.
These checks are every few seconds and consist of establishing connections via SSH to the underlying host

When the Redis process becomes unavailable for any reason, a failover is unnecessary and the process is booted back into availability instead, ensuring an even shorter downtime period whenever possible.

After our systems initially detect a problem, we confirm that the instance is truly unavailable by running several checks for two minutes across multiple network locations. This prevents transient problems from triggering a failover.

Standbys are kept up to date asynchronously. It’s possible for data to be committed on the primary instance but not yet on the standby. Typically, data loss is minimal because Redis isn’t committing the data to disk.

### After failover

1. The instance url has changed
2. App restarts with new credentials. if you're on `Premium-7` and above failover is transparent. As elastic IP doesn't change
3. New standby is re-created

HA not available until all failover conditions are meet

## Gotcha's

- Poor performance. Often due to reaching upper limit of your plan. (memory)
- Service logs and metric logs are not on fir yet.
- mini plan doesn't do any persistence. So data is lost if re-booted
- min plan also doesn't have maintenance windows.
- 4 months after deprecation date, access is restricted and instances will start to be deleted
