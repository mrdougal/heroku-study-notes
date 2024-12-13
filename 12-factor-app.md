# The twelve-factor app

https://12factor.net/

Methodology for "software as a service" applications

Co-written by Adam Wiggins - Heroku co-founder

- declarative formats for setup automation. Low barrier to entry for new team members
- clean contract with underlying operating system = maximum portability
- suitable for cloud = minimal need for serve admin
- minimal divergence between dev/production. Continuous deployment
- scale up without massive changes to tooling, architecture or dev practices

Applicable to any programming language

---

## Distilled from real world projects

Ideal practices for development

Paying attention to organic growth over time

Dynamics of collaborators working on codebase - avoiding software erosion/rot. (having app decay without making any changes) eg: avoiding specific file paths

> Slow deterioration of software over time that will eventually lead to it becoming faulty [or] unusable

1. **Codebase in source control**. Many deploys
2. **Dependencies** explicitly declared and isolated
3. **Config** every that varies between deployments defined in one place (env vars)
4. **Backing services** treat as attached resources (database, smtp etc)
5. **Build, release, run** strictly separate build and run stages
6. **Processes** execute the app as one or more _stateless_ processes (share nothing, any persistence in a db)
   - Host OS will have a process manager to handle crashes, restarts
7. **Port binding** export services via port binding vs having runtime injected by a web server
8. **Concurrency**. Scale via process model. "share nothing" can scale out. Shouldn't write PID files, have OS handle processes
9. **Disposability** fast startup and graceful shutdown. Aid in scaling. Should be robust against "sudden death". Robust queueing
10. **Dev/Prod parity** - build, release, run
    - **time gap**. Weeks or months to get code into prod. Continuous deployment to reduce gap
    - **personnel gap**. Devs write code, ops deploy
    - **tools gap**. SQLite local, PG in production
11. **Logs** treat as event streams. `stdout`
12. **Admin processes** only run as one-off processes eg: `rails console`


### Concurrency

Set config var `WEB_CONCURRENCY` (defaults to 1). Always test concurrency states in staging before shipping to production
Dyno use is charged to the second

### Disposability 

- Minimal startup time
- Can use a boot timeout tool to increase start time if needed
- Process should shutdown gracefully when process gets a `SIGTERM` signal. 
    - On web we cease to listen to `$PORT` so there are no new requests handled.
    - Have `30s` to shutdown, otherwise will get a `SIGKILL`
- Heroku has a hard limit of `30s` for a http request to complete.
    - with long polling the client needs to reconnect when connection is lost


## Logs

Logs are a stream of aggregated time-ordered events

 - collected from all running processes and services
 - no routing or output concern (that's for the logdrain)
 - no attempt to write or manage files
 - write to `stdout` as a stream

 Logplex will retain up to 1,500 lines


## One off dynos

- `heroku run`
- close session via `exit` command
- They run alongside other dynos
- Usage charged like any other dyno
- Never restart 
- Automatically stopped after 1 hour of inactivity (input and output)
    - when connection closed dyno is send `SIGHUP`


#### One off dyno's in the background
- `heroku run:detached`
- sends output to logs vs console
    - only startup and shutdown is recorded in the logs
- no connection so no timeout
- dyno will be cycled every 24 hours
- SSH keys need to added to account if on Shield Private Space

### Scheduled dynos'

- Free addon to schedule one off dynos at defined time
- No guarantee that job will run, but should be expected
    - can run a custom clock process, if critical


### Debugging dynos

> Note on fir use `heroku run:inside`

- `heroku ps:exec` makes direct connect to a production dyno. by default connects to `web.1`
- can forward ports `heroku ps:forward 9090` (for remote debugging in VSCode)
    - you may need to redeploy your application