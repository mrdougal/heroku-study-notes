# Private spaces

Is a walled garden for [security](https://devcenter.heroku.com/articles/private-spaces)

_Enterprise only_

- Network isolated environment (from other apps on the platform)
  - Can lock down inbound requests to range of IP’s
- Ensure dynos run in certain geo regions closer to customers
- Deployment in regular manner
- "A trust boundary"

### Logging

Does not use [logplex](../architecture/logging.md)

The whole space is logged not the application.

- Dev's don't deal with log configuration
- All apps (in space) consistently logged
- Easier for auditors to ensure configured correctly and will stay that way
- Log data is sent directly to

## Geography

Many **more places** vs common runtime.
Region can't be changed after creation. All apps in space are in same region

| Name      | Location                |
| --------- | ----------------------- |
| dublin    | Dublin, Ireland         |
| frankfurt | Frankfurt, Germany      |
| london    | London, United Kingdom  |
| montreal  | Montreal, Canada        |
| mumbai    | Mumbai, India           |
| oregon    | Oregon, United States   |
| singapore | Singapore               |
| sydney    | Sydney, Australia       |
| tokyo     | Tokyo, Japan            |
| virginia  | Virginia, United States |

## [Shield](./shield.md) private spaces

Includes additional features for high compliance.

## Connecting to VPC's

Can specify `CIDR` ("Classless Inter-Domain Routing") range for your private space to connect to on-premise or other VPCs.

You can't modify CIDR values after private space is created

To allow access to external services like Kafka best to use `mTLS`

## Can limit access

- To IP ranges
- By default a space trusts the internet eg: `0.0.0.0/0`

## App creation

Only `admins` can create an app
Limit who has access to space, especially production

## Rolling deploys

Can take a while for infrastructure to spin up on first deploy
Subsequent deploys will be a lot faster due to "rolling deploys"

Bit like "pre-boot" in common runtime.

- Stops and changes 25% of existing dynos at a time. Including `web` and `worker`

**Deployment and scaling is the same** as common runtime
