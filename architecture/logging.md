# Logging

https://devcenter.heroku.com/articles/logging

## Runtime logs

- App logs - Application and dependencies
- System logs - From platform infrastructure on behalf of your app
  - Restarting
  - Sleeping
  - Waking
  - Serving error page
- API - admin actions taken by you and other devs
  - Deploying new code
  - Scaling
  - Toggle maintenance mode
- Add-on - from add-ons

## Build logs

- Building
- Deploying

## Logplex

Built for collating and routing logs NOT for storage.

- Retains last 1,500 lines
- Expire after 1 week
- Add-ons available if you need more
- Can write own “log drain” https://devcenter.heroku.com/articles/log-drains

> Note that private spaces don't use logplex

## Writing to logs

- Anything to “stdout” or stderr”
  - eg “puts” in ruby or “console.log”

https://devcenter.heroku.com/articles/writing-best-practices-for-application-logs

## Viewing logs

- CLI `heroku logs —tail`
- Dashboard
- A logging add-on or your log drain
