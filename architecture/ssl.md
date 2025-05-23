# SSL on Heroku

SSL secure comms between clients and websites
TLS replaces SSL, what is actually used but SSL is used interchangeably

## TLS versions

| Version     |                                                     |
| ----------- | --------------------------------------------------- |
| SSL 1, 2, 3 | not to be used                                      |
| TLS 1       | successor to SSL 3 but now outdated                 |
| TLS 1.1     | Only use if need compatibility with older devices   |
| TLS 1.2     | Min safe level for apps where security is important |
| TLS 1.3     | Newest most secure                                  |

## On Heroku

- Inbound requests received by load balancer with SSL termination.
- Automatically enabled for `APP.herokuapp.com` common runtime
- http to https redirection happens at app layer not in router.
  - App needs to check `X-Forwarded-Proto` or `X-Forwarded-Port` request headers

## Shield Private Spaces

Shield spaces have stricter requirements for TLS. eg: TLS 1.0 can't be used with a Shield Private Space

Apps in private space need to use TLS connections with SNI extension (server name indication) Part of the TLS/SSL handshake, supported by all but the oldest browsers and OS. Is a workaround to the issue of servers hosting more than one website.

TLS handshake is part of the **routing health metrics** for private spaces
There's a routing health metric `healthy` or `degraded`
A failing handshake indicates problem with router or certificate

## Enabling SSL on Heroku

https://devcenter.heroku.com/articles/understanding-tls-on-heroku

1. **Automated Certificate Management**, use unless you need a feature it doesn't support

   - provides TLS certs no additional cost
   - certs for multiple domains
   - auto renew certs

   Doesn't

   - support private spaces with wildcard domains
   - org validation _OV_ and extended validation _EV_ not supported
   - App with internal routing
     - eg: apps that use trusted IP's to block access
   - Won't work with CDN's
   - No support for IPv6 AAAA DNS records

## If you upload your own certs

`APP.herokuapp.com` will stop https (http will still work)

## Heroku SSL (default if not using ACM)

- Upload your own SSL certs
- Free service with any paid dynos
- Can use wildcard or EV certs

Doesn't

- auto-renew
- no older browser support if they don't support SNI

## SSL Endpoint (is an addon)

- $20 month for common runtime apps
- Free with private spaces
- Only use if other options don't suit
- endpoints auto scale, but more than 150 requests sec or 2x existing requests per sec should contact support. (Might take 2 business days)
- Supports older browsers
- Can disable TLS 1.0 or 1.1
- Support for WSS (WebSocket Secure)

Doesn't

- auto-renew
- may need to contact support if spikes in traffic

There are other add-ons for SSL

### Enforcing SSL

Redirects need to happen at the application level
