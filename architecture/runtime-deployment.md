# Runtime and Deployment

## Runtimes

- Ruby
- Node
- Java
- Python
- Clojure (JAVA runtime) https://clojure.org/
- Scala https://scala-lang.org/
- Go
- PHP

## Profile

Need to tell Heroku what runtime to use. It’ll guess based on what it finds, but best to use [Procfile](https://devcenter.heroku.com/articles/procfile)

- `web`: Web server. Only process that can receive http
- `worker`: Workers for queue
- `urgentworker`: queue
- `clock`: Singleton process (scheduled jobs)
- `release`: Tasks to run before a new deployment

## Deployment

- Push to heroku `main`.
- Can also integrate with Github
  - Optionally each new branch has it’s own review app
- Heroku API can control deployment
- **Deploy to Heroku** button
  - Little to no config
- Deploy via Docker
  - `container` stack
  - Advanced use case only
    - Recommend buildpack first
      - As they include base image updates
      - Language optimisations
  - Heroku.yml
    - Setup
      - add ons
      - Config vars
    - Build
      - Link to Dockerfile
    - Release
      - Tasks to execute
    - Run
      - Process types
      - Commands to run
- Deploy [Hashicorp Terraform](https://www.terraform.io/)
  - Infrastructure as code
  - Con - Support-wise you’re on your own

## Build/Buildpacks

- **Slug** = Your app, dependences and runtime. Ready for execution
- **Dyno** = Isolated virtualised Unix container. Runs 1 process.
  - Eg: web. How many to use controlled by `Procfile`
- **Router** = Application Traffic control
  - HTTP 1.1 requests to dynos
    - Chunked responses (streaming)
    - Long polling
    - websockets
  - SSL termination
  - HTTP 2 support is coming
  - Load balancing

When new app is deployed, all current executing dynos are killed and replaced. So dyno formation is preserved.

## Dyno connection behaviour

- If there is a timeout
- Router has 1Mb response buffer

`heroku ps`
Lists what is currently running. More dynos for scaling.

Config [vars](https://devcenter.heroku.com/articles/config-vars)

Environment specific instead of an `.env` file

- Whenever config is changed, app is restarted.
- Persist between deployments
- Can set
  - via CLI
    - `heroku config:set ENCRYPTION_KEY=my_secret_launch_codes`
    - `heroku config:get ENCRYPTION_KEY`
    - `heroku config:unset ENCRYPTION_KEY`
  - Or via dashboard in “Settings”
  - Or via Heroku platform API
- Access in source via `process.env`

## Policies

- Naming
  - Only alphanumeric and `_`
  - Can not start with a digit (to ensure compatibility with many runtimes and shells)
  - Can not start with double underscore `__`
  - Can not begin with `HEROKU_` (unless set by Heroku)
- Data
  - Can’t be exceed **64Kb per app**

Running [locally](https://devcenter.heroku.com/articles/heroku-local)

To start all processes in Profile
`heroku local`

Or can start one process
`heroku local web`

- `-f` to use different Procfile
- `-e` to use difference environment file eg: `heroku local -e .env.test` (by default uses .env file)
- `-p` to use different port. Defaults to 5001
- `run` for one off command. Eg: `heroku local:run rails console`
