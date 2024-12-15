# Heroku - Continuous delivery

## Pipelines

Can include more than one app in each stage. Including production, may have an admin app running with different config. Or apps deployed to different regions…

- Development
- Review
- Staging
- Production

`heroku create --remote staging`

For staging set your env to production to reduce surprises.

Code deployed into stage as a slug. The slug is promoted through stages.
“Downstream” is term for promotion. (I think of it as going up)
 
Make sure your migrations have an “advisory lock” to prevent concurrent migrations. (They’re application level)

- If acquired in a transaction, will be released when transaction completes

## Review Apps

- Created with each PR
- “Release” command
  - DB schema setup, migrations
  - CDN uploads
  - Cache invalidation and warmup
  - If release fails, review app fails
- After release “post deploy” often used for
  - Setup OAuth…
  - DNS
  - Load seed data
  - Is only run once. Updates to the PR won’t trigger this step

## Heroku CI

- Runs your test suite
  - On Github PR
  - When merging into “main” branch

## Release phases

Certain tasks to be run before a new release of app is deployed

- Created whenever…
  - Deploy code
  - Change config
  - Modify an “add-on”
- Auto-incrementing number v1, v2 etc
- Can inspect and rollback if required
  - Rollback creates a new release
  - Rollback doesn’t effect add-ons or your app’s state
- Reason for release stated
- Can cancel
  - Need to find the one off dyno
  - Then “heroku ps:stop release.NUMBER”
