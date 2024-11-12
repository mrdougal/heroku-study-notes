# Security

## Shared responsibility & Compliance

Shared responsibility between Salesforce and us/client

### Salesforce responsible for

- Implementing and enforcing how teams accesses/ Heroku services

## CSA CAIQ "Cloud Security Alliance Consensus Assessment Initiative Questionnaire"

https://cloudsecurityalliance.org/artifacts/consensus-assessments-initiative-questionnaire-v3-1

- yes/no questions
- pen test reports

## Runtimes

- Common runtime. Multi-tenant, low to moderately sensitive data
- Private spaces. Account isolated environment, defined network boundaries. Moderate to high security.
- Private Shield spaces. Account isolated env. PCI, HIPAA geo isolated

More regions available for "private spaces"
Common runtime is either Europe (which is ireland `eu-west-1`) or US `us-east-1`

## Features

- SSO
- MFA
- Enterprise Accounts. Manage users and groups
- Lock Apps access to heroku
- Config vars
- Add on controls. Restrict access to limited add ons
- Limit access to 3rd party OAuth.
- Limit PG credentials
- Heroku Flow. Structure deployment workflow (Constant Delivery). Pipelines, Review App, CI, Github integration
- Heroku Dashboard session limits

## Logging

- Log aggregation from app, system, api, addons. Best to store in 3rd party as Heroku doesn't hold them for long
- Audit trails. Enterprise only. History of config changes
- Private space logging. Regular logging + PG logging.
- Keystroke logging in the "single use" run session.

## Data access

- Trusted IP ranges
- Private space
  - Private connection between dynos or to AWS VPC you control. (Doesn't go over public internet)
  - VPN from dynos to your hosts encrypted with IPSecs (via public internet)
  - Internal routing limit access to apps in same private space/shield
  - Private link - connect "first party addons" (Heroku PG, KVS, Kafka) to AWS VPC
  - Mutual TLS (mTLS) from external resource to Heroku PG or Heroku Kafka

## Encryption

- Automated Cert management. (ACM). Auto renew certs one month before expire (when you add custom domain)
- Cipher suites. configure suites used to negotiate TLS.
  - By default `v1.2`
  - By default traffic from Salesforce to PG via Heroku Connect doesn't need additional config
