# Heroku - Releases

To see history
`heroku releases`

To get information on a particular release.
`heroku releases:info v24`

Will list

- Reason
- Who
- When
- Add-ons

## Rollback

Can rollback to a good known version
Doesn’t effect app state

- DB
- Add-on config

_Rolling on a rolled back app is a temporary fix._ (Creates a new release)


## Custom release phase

For when you need something more than just combining the build and config

- sending  `css` `js` to a CDN
- prime or invalidate a cache
- run db migrations