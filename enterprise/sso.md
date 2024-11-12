# SSO

- Integrates with identity providers (IdP)
  - Need to support SAML 2.0
  - MFA enforcement at the IdP is required

Employees not notified when SSO is setup or disabled.

Supported out of the box

- Auth0
- Azure
- Google cloud identity
- Okta
- OneLogin
- Ping Federate
- Ping Identity
- Salesforce Identity

Others can setup in `Settings` (need to be an admin)

- Need to provide IdP certs
  - Accept SAML assertions signed under SSO certs
- Ideally sign SAML response and assertion with SHA-256
- Admins are emailed
  - 30 days, 7 days and 1 day prior to cert expiration

## First time user logs in via SSO

- Account created via IdP provisioning
- Access to resources/settings depends on default role to new users
  - Default role is `member`
  - Can adjust in `Settings`
- Create an admin role directly in Heroku in case of mis-configurations with the IdP

## Removing user from the IdP

Prevents user logging into Heroku but doesn't remove the account. So CLI access may still be possible before the API keys timeout.
Need to contact support to remove account created by automatic provisioning. :-(
