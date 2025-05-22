# Private spaces

Is a walled garden for [security](https://devcenter.heroku.com/articles/private-spaces)

Isolated network environment

_Enterprise only_

- Network isolated environment (from other apps on the platform)
  - Can lock down inbound requests to range of IP’s
- Ensure dynos run in certain geo regions closer to customers
- Deployment in regular manner
- "A trust boundary"

## Internal routing

https://devcenter.heroku.com/articles/internal-routing

Can only enable on creation. Apps with internal routing can't receive external web traffic. (It's only to their `web` process)

- Only from other apps running in Private Space
- Software on VPC-peered or VPN connected networks (VPC must have route for entire space space)
- provides access granularity per app

### Troubleshooting

- Debugging is hard with internally routed apps
- `heroku logs`
- `curl` from a `heroku run bash` session

## Logging

**Does not use [logplex](../architecture/logging.md)**

The whole space is logged not the application.

- Dev's don't deal with log configuration
- All apps (in space) consistently logged
- Easier for auditors to ensure configured correctly and will stay that way
- Log data is sent directly to
- Console sessions encrypted and need a registered SSH key

## Geography

Many **more places** vs common runtime.
Region can't be changed after creation. All apps in space are in same region

| Name        | Location                |
| ----------- | ----------------------- |
| `dublin`    | Dublin, Ireland         |
| `frankfurt` | Frankfurt, Germany      |
| `london`    | London, United Kingdom  |
| `montreal`  | Montreal, Canada        |
| `mumbai`    | Mumbai, India           |
| `oregon`    | Oregon, United States   |
| `singapore` | Singapore               |
| `sydney`    | Sydney, Australia       |
| `tokyo`     | Tokyo, Japan            |
| `virginia`  | Virginia, United States |

## [Shield](./shield.md) private spaces

Includes additional features for high compliance.

## Connecting to VPC's

Can specify `CIDR` ("Classless Inter-Domain Routing") range for your private space to connect to on-premise or other VPCs.
(CIDR = method for allocating IP addresses for IP routing)

To allow access to external services like Kafka best to use `mTLS`

### CIDR rules

- `/16` CIDR range is required for dyno VPC
- `/20` CIDR range is minimum range size for data VPC (can be increased by contacting support)
- can't overlap with `172.17.0.0/16`
- You **can't modify CIDR values** after private space is created
- defining ranges can only be done via CLI

## VPN Connections

there are some constraints

- routes must be statically configured
- VPN gateway needs to use IPSec tunnels provided by Heroku. As one might be down for maintenance
- Our VPN gateway needs to initiate the connection
- PG can't be accessed via VPN (need to use trusted IP allowlists)
- Only one VPN connection (with 2 redundant tunnels) per space
- Like trusted IP's VPC Peering doesn't allow access to data services in the space
- VPC's CIDR block must not overlap with space CIDR range
- Must have config that compatible with IPv4 CIDR
- AWS account must have permissions to make VPC peering request

[See other notes on VPN's](../integration/networking.md)

## Can limit access

- To IP ranges
- By default a space trusts the internet eg: `0.0.0.0/0`
- Trusted IP ranges only restrict access to applications in the space. Doesn't restrict CLI or dashboard access
- Trusted IP's don't automatically have access to data services
- Max of 20 ips per space but can be increased via Heroku support

## App creation

Only `admins` can create an app
Limit who has access to space, especially production

## Rolling deploys

Can take a while for infrastructure to spin up on first deploy
Subsequent deploys will be a lot faster due to "rolling deploys"

Bit like "pre-boot" in common runtime.

- Stops and changes 25% of existing dynos at a time. Including `web` and `worker`

**Deployment and scaling is the same** as common runtime

## DNS Service discovery

- All processes (not just web) can bind to ports on the private network (in a private space. Can't do this in common runtime)
- Dyno's with DNS service discovery have env vars set
  - `HEROKU_DNS_FORMATION_NAME` The round-robin DNS name for the dyno’s process type
  - `HEROKU_DNS_DYNO_NAME`
  - `HEROKU_DNS_APP_NAME`
  - `HEROKU_PRIVATE_IP`

Processes need to bind to port between `1024` and `65535`.

With `nslookup`
Naming convention of dynos in a space `[DYNO_NUMBER (optional)].[PROCESS_TYPE].[APP_NAME].app.localspace`

- Does not provide routing stack or SSL termination

## Shield databases

- Can't use dataclips
- can't use `pg:psql` from outside of the private space
- are monitored for additional intrusion detection
- `PGBackups` won't work
- Access via Trusted IP's not allowed
