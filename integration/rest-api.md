# REST API's

https://trailhead.salesforce.com/content/learn/modules/api_basics/api_basics_overview

`JSON` and `XML` payloads

For interacting with Salesforce
Easy to use (vs SOAP)

If you want to drive a UI, beast to use User Interface API (which is restful)

Every request needs **OAuth 2.0** token

- need org and user with API access
  - user must have "API Enabled" permission
- not all orgs have api access on by default.

- app to app
- designed for use with "salesforce objects"

## [Auth flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_oauth_and_connected_apps.htm) (needs a connected app)

- A connected app (on behalf of client app) requests access to resource
- response server grants access token to connected app
- resource server validates access tokens and approves access to resource
