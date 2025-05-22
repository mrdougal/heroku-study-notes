# Heroku - Scaling

`P95` response. 95% responses have a time of less than the value.

## Limits - default

Can submit a request if need to change this

- Capped at 100 processes total (web, workers etc)
- Eco or Basic have only one process (no auto-scaling)
  - Can only have 2 con-current processes. Eg: web and worker
- Performance, Private or Shield can’t have more than 10 dynos for each process type

## One-off dyno limits

- Eco - 1 dyno
- Basic - 50
- Standard - 50
- Performance - 5
- Private - 5
- Shield - 5

## Bump dyno’s via…

- Dashboard
  - Resources tab
  - Edit the dyno type `web`
- CLI
  - `heroku ps:scale web=3`
  - Can change multiple processes at once
    - `heroku ps:scale web=3 worker=5`
  - Can also type process type
    - `heroku ps:scale web=3:performance-l`

## Autoscaling

Requires your app to have little variance in response time.
Only available to below dynos…

- Standard
- Performance
- Private
- Shield
  (There are 3rd party options if the built in one doesn’t fit)

Optionally can be emailed when dyno count reaches upper limit. (Max one email a day)

> Manually setting scaling cancels auto-scaling.

### Logic

- Uses p95 time to determine when to scale.
- Data from past hour to calculate number of dynos
  - Doesn’t include Websocket traffic
- One dyno added per autoscaling event
  - Scaling events **1min apart**
- Scales down less aggressively
  - 3 mins before scaling down
- Downstream bottlenecks might be the issue, in which case scaling won’t help. Might even make it worse
  - If 20% requests fail, autoscaling stops.

### Notifications

- Auto scaling event in log
- Webhooks

### Gotchas

- DB might be the bottleneck
  - Connection pools might help
- Simulate via load testing
- Use threshold alerting to monitor health
