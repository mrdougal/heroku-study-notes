# Chatops

https://devcenter.heroku.com/articles/chatops

Leverage Heroku Pipelines in Slack. Deployments or promotions

- When deploying via ChatOps, commit status is checked before deploying
- To install the Slack app need permissions to install the app
- Auth via OAuth
- Need to be owner or collaborator on app
    - or if in enterprise team need `deploy` permission
- Github needs to be connected

- doesn't support private slack channels or Github enterprises

## Commands (in Slack)

`/h login` 
`/h deploy PIPELINE_NAME to STAGE_NAME` eg stage  = staging or production
`/h deploy PIPELINE_NAME/BRANCH_NAME to STAGE_NAME` deploy a specific branch

`/h deploy! PIPELINE_NAME to STAGE_NAME` force deploy (if have flaky tests and code fails tests)


`/h promote PIPELINE_NAME` by default will promote to production
`/h promote PIPELINE_NAME from UPSTREAM_STAGE` can use an upstream stage to production


If have multiple apps in a stage, need to specify which app
`/h promote PIPELINE_NAME from UPSTREAM_STAGE/APP_NAME to DOWNSTREAM_STAGE`
`/h promote PIPELINE_NAME from UPSTREAM_STAGE/APP_NAME to DOWNSTREAM_STAGE/APP_NAME`


`/h releases PIPELINE_NAME` See what's been released
`/h releases PIPELINE_NAME in STAGE_NAME` stage being `staging` or `production`



`/h info PIPELINE_NAME` gives info on pipeline
`/h pipelines` list of deployable pipeline

`/h route PIPELINE_NAME to #CHANNEL_NAME` Route notifications to a channel. (pipeline events initiated from CLI are not displayed)

`/h route` list pipelines routed to a channel
From within a channel `/h route` select pipeline from response


## Receiving alerts

If p95 threshold is triggered will receive alerts in channel