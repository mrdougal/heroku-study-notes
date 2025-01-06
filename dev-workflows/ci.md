# Continuous Integration

Values stored in `app.json` (like what is used to run on Heroku)

- `scripts` override commands on Heroku CI
  - `postdeploy` once off setup tasks after app is created. Can be string or an object
    - if an object `command` to run and `size` of dyno to run the command
  - `pr-predestroy` run a cleanup task after a review app is destroyed. Value can be string or object like postdeploy
- `add-ons` what add-ons to use for temporary deployments
- `env` env vars specific for test runs
- `buildpacks` array of buildpacks
- `formation` key-value for process type. (values are objects like quantity, size)

- Tests run with every push to Github
  - does not run on pipeline promotions, or direct deploys on pipeline
  - all CI runs in common runtime, even if app is in a private space. (tests won't be able to reach resources in the private space)
- Works with any pipeline
- Connect repo via `Settings` in dashboard
- If using common testing framework, may work out of box
  - Can configured via `scripts` section in `app.json` manifest
    - `test-setup`
      - Install linters
      - Seeding a db
    - `test` likely the same as what you use to test locally
      - eg: `bundle exec rspec` or `npm test`
    - Manifest used to determine add-ons
      - Many have plans for temporary deployments. (For test environments)

## Configs

- CI doesn’t inherit config vars from parent app (unlike review apps)
- Sensitive configs can be set via dashboard vs being in `.env`

## Debugging

`heroku ci:debug`
Don’t have to push to GitHub to trigger

## Good to know

- CI doesn’t run on pipeline promotions
- CI always runs in the common runtime
  - Event if parent app is in a private space

## Testpack

The magic that makes it `just work™`
Supplements the buildpack

- `bin/test-compile` transform app into testable app
  - Like `bin/compile` but skips production steps. Like minification
- `bin/test` run the tests. Typically same command as when you run tests locally
  - Exit code of `0` is success

Cost is free. It used to cost $10 a month
