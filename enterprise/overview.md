# Enterprise

[Also enterprise security](./enterprise-security.md)

All dyno

- Software as a service - Salesforce
- Platform as a service (PaaS) - Heroku
- Infrastructure as a service. AWS

What it can do…

- Bespoke customer website
- Custom APIs
- Data manipulation

## Heroku Connect

[See page on connect](./connect.md)

## Single Sign-On

Varies id providers supported. See [SSO](./sso.md)

- Salesforce Identity
- Okta
- Bitium
- Ping

## Private spaces

In a walled garden for security. See [private-spaces](./private-spaces.md)
https://devcenter.heroku.com/articles/private-spaces

- Network isolated environment (from other apps on the platform)
  - Can lock down inbound requests to range of IP’s
- Ensure dynos run in certain geo regions closer to customers

## Shield private spaces

https://devcenter.heroku.com/articles/shield-private-space

- High compliance
- Strict TLS enforcement
- Keystroke logging on `heroku run`
- Doesn’t use `logplex`
