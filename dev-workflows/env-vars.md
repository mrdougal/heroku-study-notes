# Environment variables and configuration

## CLI

- `heroku config` view all config
- `heroku config:get DB_URL` view a config
- `heroku config:set DB_URL=` set a config
- `heroku config:unset DB_URL` remove a config

## Dashboard

In `Settings`

## API

Can use platform api to set via est.

## Accessing

- `process.env.DB_URL` in Node
- `env['DB_URL']` in ruby

## Policies

Naming (to ensure compatibility with many runtimes and shells)

- Only alphanumeric and `_` (no hyphens)
- Can not start with a digit
- Can not start with double underscore `__`
- Can not begin with `HEROKU_` (unless set by Heroku)
- Whole config can’t exceed `64Kb` per app
