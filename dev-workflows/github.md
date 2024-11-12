# Github integration

Can build and deploy _if build is successful_

## Manual deploy

- Deploy from any branch

## Automatic deploy

- From specific branch
- Optionally wait for CI to pass

## Review apps

- Deploy application for each PR raised.
  - Review app deleted when PR closed
  - Can be set to be deleted after 1,2,5,14 or 30 days of inactivity
- Each have unique url
- Requires Heroku Pipeline and Github integration
- May need to configure `app.json` https://devcenter.heroku.com/articles/app-json-schema
  - Describes “Add-ons”
  - “Build packs”
  - “Env” vars
  - “Formation” - quantify./size
  - “Image” - if using Docker
    - Need to also define “heroku.yml” https://devcenter.heroku.com/articles/build-docker-images-heroku-yml
      - Setup
      - Build
      - Release
      - Run
  - “Logo” - optional needs to be square. svg, png or jpg
- Select URL app terns. Random vs predictable
- Permissions
  - All Heroku admin users will have access
  - “Members” can view, deploy and operate. Can’t promote
  - Token of user who created integration is used to create apps
- Private spaces
  - Need to specify in the “app.json”
- Costs
  - Charge same cost as regular app
  - To the second
- There’s an API for this
- Git submodules don’t work
