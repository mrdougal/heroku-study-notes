# The twelve-factor app

https://12factor.net/

Methodology for "software as a service" applications

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
