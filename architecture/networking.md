# Networking

## Timeouts

- 30secs to send response
- After that rolling 55sec window
- All HTTP verbs supports
  - Except CONNECT

https://devcenter.heroku.com/articles/http-routing

- Load balancer, with SSL termination
- Request sent to routers
- Clear path through stack means can support
  - Chunked
- HTTP 2 available in “Public Beta”
  - Can opt into via “heroku labs:enable http-routing-2-dot-0 -a <app name>”

## Websockets

- RFC-6455 is supported on Heroku (2011)
- HTTP timeouts still apply
  - Client or server can prevent timeouts by sending ping over connection

Support for http keep alive

## Connection behaviour

- Router passes connection to dyno
- If dyno can’t connect within 5secs - dyno is quarantined for up to 5secs.
  Quarantine only applies to a single router, other routers can forward to the dyno.

When connection is refused or times out, router attempts to connect to another dyno. After 10 failed connections (or fewer if you have less than 10 web dynos) router returns “H19 connection timeout” or “H21 Connection refused” error

But… in privates spaces

- Router doesn’t forward connections if a dyno refuses
  - Will raise H12 [“Request timeout” error](https://devcenter.heroku.com/articles/error-codes#h12-request-timeout)
