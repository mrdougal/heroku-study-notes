# Stacks

Multiple build packs
https://devcenter.heroku.com/articles/using-multiple-buildpacks-for-an-app

JS for assets, ruby for server

Set via `cli`
`heroku buildpacks:add --index 1 heroku/nodejs`

`heroku buildpacks:set heroku/ruby`

Define the `primary` build pack last. So that defaults for that language are applied

## Migrating an app to another region

Verify

- Update your CLI `heroku update`
- Verify the app by “forking” it via CLI `heroku fork`
  - This is a plugin to the CLI

## Cut over live traffic
