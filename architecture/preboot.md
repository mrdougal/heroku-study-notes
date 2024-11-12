# PreBoot

https://devcenter.heroku.com/articles/preboot

Ensure new dyno’s are up and receiving traffic before shutting down old dynos.
You will have two versions of the same code running at the same time. Need to make sure your code and database config can handle this

Need to be enabled explicitly per application. (Enabling does not trigger a restart)

`heroku features:enable preboot -a <myapp>`

## On deployment

- New dyno’s will start soon after compile
- ~3mins after deploy http will be routed to new dynos
  - 2mins after app boot timeout (default limit is 1min)
- Then old dyno’s will be shut down

## On restarts / automated restarts

- Logs will show new dynos starting
  - New dyno’s will appear in `heroku ps` (old dyno’s won’t though)
- New dyno’s will start receiving requests as soon as they can. Old and new will be running together
- 4-6mins later old dyno’s will be shut down

## Gothca’s

- For 1-2mins old dynos will be serving requests but `heroku ps` will not list the old dynos
- If have a bad dyno can kill immediately “heroku ps:stop”
  - As sending restart won’t kill it for 3+mins
- Running two versions of your code… db issues
  - May trigger add-on limits - for simulations connections
    - Try connection pooling (if using PG)
- Possible to have request go to both dynos (very short window)
- Dyno formation changes aren’t allowed using release
- If restart is triggered by a config change…
  - Need tot be aware that old dynos will be running with old config
  - Avoid with below
    - Mini Hero Key-Value Store
    - Essential Heroku Postgres
    - More expensive plans have stable addresses
