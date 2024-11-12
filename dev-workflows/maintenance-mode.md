# Maintenance mode

Set the page to serve, a static url. Typically an S3 bucket but can be anywhere that serves web pages.
`heroku config:set MAINTENANCE_PAGE_URL=//s3.amazonaws.com/<your_bucket>/your_maintenance_page.html`

## Enable via CLI

`heroku maintenance:on`

- Does not alter dyno formation
  - You’ll still be billed for hours
  - Heroku router blocks requests coming in
    - Unless you’re in a private space. Don’t scale web to 0, as this will prevent the maintenance page being served. (As the router is on the web dyno’s instance)
  - So can still run worker dyno for queues, or db migrations

If want to prevent db transactions, scale formation down to `0`
`heroku ps:scale worker=0`

Access logs will show [`h80` error code](https://devcenter.heroku.com/articles/error-codes#h80-maintenance-mode) (to state that the user was served a maintenance page)
