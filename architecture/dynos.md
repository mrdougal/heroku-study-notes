# Dynos

Heroku managed Linux container

- Web dyno
- Worker
- One off
  - Db migrations
  - Can be scheduled

## Benefits

- Managed
  - Image updates
  - Language optimisations
- Scalable
  - Vertically by changing Size
  - Horizontally via adjusting Quantity
    - Not available on eco and basic plans
- Isolated and secure
- Free SSL
- Custom domain
- Logging
- Ephemeral file system (short lived)
  - Files written only visible to dyno
  - FS reset on restart

## Metrics

- Memory usage
- Gathered per process type
- Resolution
  - 1 min (past 2hrs)
  - 10 mins
  - 1 hr
  - 2 hours
- Web dyno collects…
  - Response time
    - median.( 50% under quoted time and 50% over)
    - 95th Percentile
    - 99th Percentile
    - Max. Max response time
  - Throughput
    - Nº requests by status code
- All dynos collect 1 - 10 min increments
  - Memory use
  - Total memory
  - RSS (amount memory in RAM)
  - Swap
- Dyno load
  - Quotes number of threads

## Events

- Errors
  - Critical - red
  - Warning - yellow
  - Info - grey
- Platform status events
- Config change
- Deployments
- Scaling
- Restart

## Alerts

Configure thresholds for alerts

- Max 95th response time
  - Default 50ms
- Percent of failed (500) requests
- Email or pagerduty

## Autoscaling

[PreBoot](https://devcenter.heroku.com/articles/preboot)
Ensure new dyno’s are up and receiving traffic before shutting down old dynos.
You will have two versions of the same code running at the same time. Need to make sure your code and database config can handle this

Need to be enabled explicitly per application. (Enabling does not trigger a restart)

`heroku features:enable preboot -a <myapp>`

## Rolling Deploys
