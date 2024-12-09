# Heroku Architect

## User management

- Four levels
  - **Admin** - full control
  - **Member** - view members and admins in team, transfer apps to the acc, can't join locked apps
  - **Viewer** - view apps, pipelines, spaces, users, resources
  - **Collaborator** - external heroku user granted access to one or more apps within team (e.g. contractor)

### Access controls

#### Privileges

[Permissions Cheat Sheet](https://devcenter.heroku.com/articles/app-permissions)

| Privilege Set | Capabilities                                                                                           |
| ------------- | ------------------------------------------------------------------------------------------------------ |
| view          | See that the app exists and view details about it.                                                     |
| deploy        | Deploy code, run one-off dynos, add or remove free add-ons, and view and edit configuration variables. |
| operate       | Change the dyno formation, add or remove paid add-ons, and restart the app.                            |
| manage        | Add or remove users, transfer the app, and delete the app.                                             |

## Heroku Connect

- syncs Salesforce <=> Heroku postgres
- avoid need to use SF API
- not real-time, but close to
- Heroku Connect can only be used with Salesforce editions that have API access. Some Salesforce plan types, including trial versions, don’t have API access by default and can’t be used with Heroku Connect.
- Uses SSL
- Shield Connect meets HIPAA requirements - must have Shield pg and Connect
- Can have multiple Connect instances for single app
- Choose sufficient Heroku Postgres plan - at least `*-4` plan for production, lower plans experience perf issues
- create separate dedicated integration user in SF to use with Connect, with `View All Data` permission to eliminate SF perm checks
- minimize locks on SF records
- Plans
  - `demo` 10k rows
    - min polling interval 10 mins
    - trigger log archived 7 days instead of 31
  - `enterprise`
    - paid plan, can upgrade to from `demo`
    - previously the `daketsu` plan
  - `shield`
    - only syncs to Shield postgres plans
- automatically chooses the most efficient transfer method reading/writing
- uses postgres trigger log to send data to SF
- custom triggers not officially supported
- Data mismatch
  - SF trims extra spaces from start and end of strings
  - if string value is empty SF API sends it as NULL
  - Postgres stores numbers as a `double` column, 15-digit precision, SF values exceeding this have their precision reduced
- Postgres connections - 1 connection per mapping to read records, 1 additional shared write connection for writes e.g. 20 mappings with read-write = 21 used connections
  - **Writing**
    - **Ordered Writes** and **Merged Writes** algorithms - The Ordered Writes algorithm is a prerequisite for using the Bulk API. It’s not possible to perform bulk writes to Salesforce when using the Merged Writes algorithm.
    - **Ordered Writes**
      - preserves order of changes from the trigger log, each change is distinct operation
      - rapid updates to same record can result in slower sync speeds
      - failed INSERT causes all subsequent updates to fail, holds up queue until resolved
    - **Merged Writes**
      - condenses and reorders changes from the trigger log to maximize records per SOAP message
      - uses the latest update, not all previous
      - not guaranteed to work for all dependencies or relationships, circular dependencies known issue
      - attempts to resend failed inserts on an update
      - don't use with Apex triggers or process rules in SF
    - auto uses bulk when
      - configured to use the Ordered Writes algorithm
      - unique identifier specified for mapping
      - 2000 - 10,0000 changes of the same type (`INSERT`, `UPDATE`, `DELETE`)
      - API version is set to v39 or higher
    - auto uses SOAP when
      - fewer than 2000 records
      - Bulk API conditions aren't met

#### SOAP AND Bulk API

- **SOAP API**
  - used for tasks upto to 10,000 records
  - does up to 200 records per SOAP message
- **Bulk API**
  - used for large data sets, uses async processing to batch records.
- intially loading data from SF org
- reloading all data into an existing mapping
- reading changes from SF org
- admin tasks such as counting records and querying mapping fields
- used when writing data to SF (lower threshold than 10k)
- requires "View All" or "View All Data" permissions, which is scoped at the individual object level.
- supports reading of all standard and custom objects, except those without required attributes such as `SystemModStamp` - Heroku detects amd suppresses the unmappable objects.
- **SOAP/Bulk API** calls made by Heroku Connect do not count towards API request limits, They're counted in your org, but don't count towards API license utilization

#### **Streaming API**

listens for changes in SF, used with Push Topic notifications for mappings that use [accelerated polling](https://devcenter.heroku.com/articles/mapping-configuration-options#accelerated-polling-of-salesforce)

- Push Topics created by Heroku Connect have a `hc_` naming prefix, for example `hc_123`, and automatically is deleted either when you switch to polling mode or delete the mapping.
- Push Topics created by Heroku Connect count towards the ‘Maximum number of topics (PushTopic records) per organization’.
- Heroku Connect’s Streaming API event consumption is not included when you check your current Streaming API event usage in Salesforce

#### API Call Usage

For changes to records being read from Salesforce, Heroku Connect uses:

- the SOAP API in batches of up to 2000 records OR
- the Bulk API in batches of up to 150,000 records

For changes to records being written to Salesforce, Heroku Connect uses:

- the SOAP API in batches of up to 200 records OR
- the Bulk API in batches of 2,000 to 10,000 records

#### Sync performance factors

- Object size
- High volume changes
- Using an ORM that 'touches' or saves non-existent changes to records
- Overall db load

## Private Spaces

- Network isolated dyno's
- Can choose the geographic location of dynos
- Good for regulated industries such as healthcare, life sciences, and financial institutions

## Shield Private Spaces

- additional cost over common runtime or private spaces
- Only `shield` type dynos can run in shield private spaces
- Shield dynos have ephemeral encrypted file system
- small, medium, large dynos
- shield postgres plans, store regulated data that cannot be stored in `private` Heroku Postgres plans
- shield Heroku Connect plan
- shield Kafka plans
- all input typed into interactive `heroku run` session is logged, for auditing
- Can manage [logging at the space level](https://devcenter.heroku.com/articles/shield-private-space#keystroke-logging), single log drain for all apps in the space
- stricter enforcement for TLS termination, TLS 1.0 cannot be used to connect to Private Shield space apps
- `heroku pg:psql` and downloading pg backups is disabled
- `heroku redis:cli` and any other external connection to Shield KVS is disabled

### Create Shield Private Space

specify the --shield option:
`$ heroku spaces:create my-shield-space-name --shield --team my-team-name`

To enable Private Space Logging, specify a log drain URL when creating the space:

`heroku spaces:create my-shield-space-name --shield --team my-team-name --log-drain-url https://mylogdrain.com`

**_Private Space Logging cannot be enabled after a Private Space has been created._**

To create a one-off dyno, you must first add an SSH key to your user account (this only has to be done once)

## Add-ons

Extend Heroku Apps with features and services, such as:

- Data stores
- Logging
- Monitoring
- Content Management
- dyno management/scaling
- set config vars
- Each add-on has one or more plans, each plan has its own price and features - integrated into monthly billing

4 Step process

1. Initiate provisioning - via **Install** button or CLI
   `$ heroku addons:create addon-name`
2. Heroku requests service provisioning
3. Add-on returns a resource URL - app uses this to access add-on resources
4. Heroku rebuilds the app - adds add-on's returned URL as a config var

## Monitoring

### Metrics

Metrics captured for **ALL** dynos:

- **memory usage** - quota, total, RSS, swap
- **load** - average, max
- **Events** - Errors, platform status events (incidents, maintenance), config vars change, deployments, scaling, restarts

Metrics gathered for **Web dynos** only:

- Response time
- Throughput

### Production Check

Tests your app against recommended criteria, run from Dashboard actions

- **Dyno redundancy** (recommend 2 web dynos for mission critical apps)
- **Heroku Postgres** Standard tier or higher. Premium, Private, Shield include High Availability with auto failover
- **Visibilty and monitoring** App monitoring (e.g. New Relic), Log monitoring (e.g. Papertrail)

### Logging

- **Runtime Logs**
  - **App Logs** logs generated by app and code)
  - **System logs** Heroku platform
  - **API logs** logs actions take on app, e.g. deploying, scaling, maintenance mode
  - **Add-on logs** messages from add-on services

`$ heroku logs -n 1500` - 1500 lines of logs
`$ heroku logs --tail` - real-time tail
`$ heroku logs --dyno router` - specific dyno
`$ heroku logs --source app` - filter app only logs

- Heroku uses Logplex to collect logs from all apps processes
- Log drains - Syslog (forward logs to external server) and HTTPS drains (log processing)

## Dynos

`$ heroku ps`

- **Web dynos** run app code, handle http reqs and serve response
- **Worker dynos** run background jobs, queues, and timed jobs
- **One-off dynos** temporary, attached or detached - for task such as db migrations and console sessions.
- vertical and horizontal scaling
  - `$ heroku ps:scale web=3:performance-l`
  - `$ heroku ps:scale worker=standard-2x`
- **Autoscaling** Only available for Performance, Private, and Shield-tier dynos

## Configuration and Config Vars

- Whenever you set or remove a config var using any method, your app is restarted and a new release is created
  `$ heroku config` **view**
  `$ heroku config:get GITHUB_USERNAME` **get**
  `$ heroku config:set GITHUB_USERNAME=joe` **set**
  `$ heroku config:unset GITHUB_USERNAME` **remove**
- Get/Set/View/Remove from Settings tab of Heroku Dashboard UI
- Can use REST API also

## Buildpacks

`$ heroku buildpacks:set heroku/ruby`

With multi buildpacks, primary should be the **last**:
`$ heroku buildpacks:add --index 1 heroku/nodejs`

View buildpacks:

```
  $ heroku buildpacks
  1. heroku/nodejs
  2. heroku/ruby
```

## Runtimes

1. Common runtime - used for base dynos and eco **US and EU only**, shared hardware, runs in containers
2. Private spaces - isolated, geo-diverse

## Stacks

A stack is an operating system image that is curated and maintained by Heroku. Stacks are typically based on an existing open-source Linux distribution, such as Ubuntu. Heroku applications target a specific stack, and buildpacks are responsible for transforming an app’s source code into an executable package that is compatible with that stack.

- Current Heroku-24

```
$ heroku stack
=== ⬢ example-app Available Stacks
container
heroku-20
heroku-22
* heroku-24
```

## Releases

- starts at v1, increments every release, also when config vars changed or add-ons added
- Supports rollback releases `heroku rollback v2`
- Logs available from the **Activity** tab (**View Release log** link on a release)
- check releases via `heroku releases`

```
heroku releases
=== example-app Releases - Current: v52
v53  Deploy ad7c527 release command failed        example@heroku.com
v52  Deploy b41eb7c                               example@heroku.com
v51  Deploy 38352d3                               example@heroku.com
```

## Pipelines

group of Heroku apps that share same codebase. Each app represents a CI stage:

- Development
- Review
- Staging
- Production - release phase runs during this stage (has a 1 hr timeout)

## Review apps

Review Apps allow you to propose, discuss, and decide whether to merge changes to your code base. For Heroku apps connected to GitHub, Heroku can manually or automatically spin up a temporary test app on a unique URL for every open pull request (PR). The temporary app auto-updates on every commit, so instead of guessing about what the code does, reviewers can actually try the changes in a browser.

- uses `app.json`
- auto destroyed when PR closes

## Release Phase

Enables running of tasks before a release is deployed to production, eliminating maintenance windows and reducing deployment risk. Use Release Phase to migrate a database, upload assets to a content delivery network (CDN), invalidate a cache, or run any other task that your app needs to be ready for production. If a Release Phase task fails, the new release isn’t deployed, leaving the current production release unaffected.
Common tasks include:

- Send CSS, JS, and other assets to a content delivery network (CDN) or AWS S3 bucket.
- Prime or invalidate cache stores.
- Run database schema migrations.

To specify tasks, add them to the app’s **Procfile**. Add a release process type to the file, along with the command to execute. e.g.
`release: python manage.py migrate`

## Heroku ChatOps

Heroku ChatOps uses the power of Heroku Pipelines to bring a collaborative deployment workflow to Slack. It enables developers to deploy to staging or promote to production from Slack. With Heroku ChatOps, teams can track all code changes within their Slack channel. Pull request notifications, merges, and CI build results all show up in Slack. There’s no context switching to see build results or check if promoting to production was successful.

## Heroku CI

- supports parallel tests on up to 32 dynos (they share same db instance provisioned for test run)

### Heroku CI In-Dyno Databases

- only work in Heroku CI
- only for Postgres and redis addons
- database queries don't pass over network
- less restrictions (e.g. row limits, connection limits, features)
- ephemeral - only for life of dyno

## Heroku Flow

brings together Heroku Pipelines, Review Apps, Release Phase, Heroku CI, and GitHub and Slack integrations for a simple visual code-to-production workflow.

## Data

### Heroku Postgres

- If your Heroku Postgres database exceeds your plan allotment, you receive a warning email with directions on how to fix the issue

#### Dataclips

- Heroku Dataclips enable you to create SQL queries for your Heroku Postgres databases and share the results with colleagues, third-party tools, and the public. Recipients of a dataclip can view the data in their browser and also download it in JSON and CSV formats.
- To share a dataclip with the public, click Enable next to Shareable Links to generate a unique URL
- Disabling the URL invalidates the existing shareable links and you can generate new ones. All URLs with the old shareable links immediately stop working.
- Dataclip results can be downloaded in CSV and JSON formats.
- You can share dataclip results with individual Heroku users, along with Heroku Teams that you belong to.
  ##### Limits and restrictions
  - Dataclips can be created for any Heroku Postgres databases except Shield Tier plans.
  - A dataclip can return up to 100,000 rows.
  - A dataclip’s results can be at most 104,857,600 bytes in size.
  - Dataclip results that are larger than 26,214,400 bytes in size are downloadable, but you can’t explore the results in your browser.
  - Dataclip queries time out after ten minutes.
  - You can’t share dataclip results with Heroku Teams that you don’t belong to.
  - Unauthenticated users retrieving results from the .csv or .json endpoints are limited to 30 requests per minute per IP.

### KVS

Heroku Key-Value Store (KVS) is an in-memory key-value data store, run by Heroku, that is provisioned and managed as an add-on. Heroku Key-Value Store is accessible from any language with a Redis driver, including all languages and frameworks supported by Heroku. KVS is compatible with both Redis and Valkey configurations.

### mTLS

Heroku provides a CLI plugin for managing mutual TLS for Private and Shield Heroku Postgres and Apache Kafka on Heroku add-ons.

- Apache Kafka on Heroku add-ons have mTLS enabled by default and users can’t disable it. You can view certificate info for Apache Kafka on Heroku add-ons in the app’s config vars and not via the commands shown in this section.

### Connecting Heroku Data Services to MuleSoft

MuleSoft is an Integration Platform as a Service (IPaaS) for connecting multiple systems and services together so they can be accessed and managed from one central interface.

- allows connection of Heroku Data Services (Heroku Postgres, Apache Kafka on Heroku, and Heroku Key-Value Store) to the MuleSoft platform.

### Kafka

Apache Kafka is a distributed commit log for fast, fault-tolerant communication between producers and consumers using message-based topics. Kafka provides the messaging backbone for building a new generation of distributed applications capable of handling billions of events and millions of transactions. Kafka is designed to move large volumes of ephemeral data with a high degree of reliability and fault tolerance

Provides:

- Elastic Queuing
- Data Pipelines & Analytics
- Microservice Coordination
- CLI - The Kafka CLI plugin requires Python, and doesn’t work on Windows without additional configuration.

### Streaming Data Connectors - Change Data Capture (CDC)

- Streaming data connectors only work if your Private or Shield Apache Kafka on Heroku add-on and Private or Shield Heroku Postgres add-on are in the same Private or Shield Private Space.
- PG -> Kafka

## Security

Heroku has leveraged the Cloud Security Alliance’s Consensus Assessments Initiative Questionnaire (CSA CAIQ) as a framework to help customers better understand this delineation of responsibility.

Heroku offers three application runtimes to allow you to meet different data sensitivity requirements:

- [Heroku Common Runtime](https://devcenter.heroku.com/articles/dyno-runtime#common-runtime): Secure multi-tenant environment for low to moderately sensitive data type that provides essential data security features
- [Heroku Private Spaces](https://devcenter.heroku.com/articles/dyno-runtime#private-spaces-runtime): Account isolated environment with defined network boundaries for moderate to high data types that provides additional data security and geographic isolation features
- [Heroku Shield Private Spaces](https://devcenter.heroku.com/articles/shield-private-space): Account isolated environment with defined network boundaries for highly regulated data types such as PCI or HIPAA data that provides enhanced data security and geographic isolation features

### Authentication & Access

- SSO
- MFA - mandatory Heroku platform security feature
- Enterprise Accounts and Enterprise teams
- Lock apps - prevents access to members without 'Manage' permission
- Config variables - can store creds
- Add-On Controls - control access to add-ons
- Limit Access For Third Party OAuth to Apps
- Heroku Postgres Credentials - can add roles to apps limiting ability to Select, Insert, Update etc
- Heroku Flow - structured release experience
- Dashboard Session Length Limits

### Logging and Monitoring

- Logging & Add-On Providers - aggregate short lived logs
- Audit Trails - Heroku Enterprise Audit trails history of config change events
- Private Space Logging - Shield private space feature
- Keystroke Logging - Shield private space feature - logs all keystrokes to Heroku run sessions

### Advanced Heroku App & Data Access Methods

- Trusted IP Ranges - restrict Private and Shield spaces to only approved IP ranges
- Stable Outbound IP Addresses - Private and shield spaces stable set of IP addresses
- Private Space Peering - Private and Shield dyno connection over internal AWS VPC
- Internal Routing - Restrict app communication to same Private Space or Shield space
- PrivateLink - PG, KVS, Kafka connect to VPC
- Mutual TLS (mTLS)

### Encryption Options

- Automated Certificate Management (ACM) - auto renew certs one month before expiry
- Cipher Suites - Private and Shield Private Spaces config cipher suites

### Data Backups

- Postgres Rollbacks - rollback pg db to a previous point in time during last 4-7 days
- Postgres Backups - configure apps to take manual or scheduled backups

## Heroku Enterprise

When you sign up for Heroku Enterprise, your license includes:

- Dyno units and add-on credits for each month.
- Features such as Enterprise Teams and fine-grained access controls.
- Access to commercial plans for Heroku Connect.
- Access to Private Spaces, providing strong network-level isolation for apps that must meet strict security and governance requirements.
- Access to Shield Private Spaces, offering additional compliance features for applications with strong compliance requirements.

### Dynos

All professional dyno types are available in Heroku Enterprise. Professional dynos: (`performance-` plans)

- Support multiple worker process types
- Enable horizontal and vertical scalability
- Include application metrics
- Offer faster builds and preboot
- Basic dynos are also available, unlike professional dynos, basic dynos don’t have horizontal scalability, preboot, or language runtime metrics.
- Eco not available with Heroku Enterprise
- 1 dyno unit = 1 standard-1x dyno running for a month (performance-l = 16 dyno units)
- In common runtime apps default to Basic dynos, Private spaces the default is private-M, for Shield it is Shield-M
- Can only mix standard and performance dynos in CR, can't use Private dynos, Basic dynos can't be mixed with other types

### Private Spaces

- PG and KVS are purchased via add-on credits
- **Heroku Private Spaces** Purchased separately as part of Enterprise license vis SKU
- **Shield Private Spaces** also purchased individually via SKU
- **Heroku Connect** and **Shield Heroku Connect** can also be purchased via Connect/Shield Heroku Connect SKU as part of Enterprise license

### Add-on Credits

- One Credit = `$1` worth of add-on services
- **Types**
  - **General add-on credits** - used for all Heroku & partner-provided add-ons, can't be used for Heroku Connect
  - **Heroku Data add-on credits** - used for Heroku-provided data add-ons Heroku PG/KVS/Kafka
  - **Partner add-on credits** - used for all non-Heroku provided add-ons

## Enterprise

### SSO

- SSO is available only for Heroku Teams and Heroku Enterprise customers.
- Using SSO, an employee logs into Heroku using your identity provider’s interface instead of the Heroku login page.
- Heroku doesn’t notify your employees when SSO is set up, changed, or deactivated for your organization.
- The first time a user logs in to Heroku via the IdP, a Heroku account gets created via automatic IdP provisioning, default role for new users is `member`

#### Prerequisites for SSO with Heroku

- Your company’s identity provider (IdP) must support the SAML 2.0 standard.
- You must have administrative permissions on the IdP.
- You must enforce multi-factor authentication (MFA) at the IdP-level.

#### IDPs with Built-in SSO support for Heroku

- Auth0
- Azure
- Google Cloud Identity
- Okta
- OneLogin
- Ping Federate
- Ping Identity
- Salesforce Identity

### Private space trusted connections

Using IP restrictions, exclusive trust may be established between Heroku Private Spaces and Salesforce

#### Salesforce => Heroku apps

Frequently, apps running on Heroku should be accessible only to Salesforce. A popular use-case is a Heroku app providing HTTP/REST query interfaces to custom Apex or Lightning components. If an API is not intended for public consumption, then best to block public access.

- **Allow incoming Salesforce traffic** - Set all Salesforce IP ranges as Trusted IP ranges for the Private Space.
- **Prevent public traffic** - remove the default entry 0.0.0.0/0 from the Trusted IP ranges for the Private Space.

#### Private space peering

Private Space Peering enables you to establish a private network connection between dynos running in a Heroku Private Space and an AWS VPC you control. This connection does not traverse the public Internet.

### Managing Enterprise Teams

- Users who don’t have the Manage permission for the Enterprise Account can’t see or access the 'All teams' tab that shows all Enterprise Teams.
- Enterprise Team names are unique across all Enterprise Accounts.
- users with `admin` or `member` permissions can create apps in team
- can Transfer apps to team in Settings -> 'Transfer Ownership'
- Bulk Transfer apps via Settings -> 'Bulk Transfer Apps - Transfer Apps' button
- Create Spaces - each Space costs $1000 in Add-on Credits/month (pro-rated to the second)

### Enterprise Accounts usage

- Usage dashboard - user needs `Billing` permission to view
- can export via CLI - Enterprise Accounts CLI Plugin needed
- can download via csv

## Architecting Apps

### Reference Architecture: Event-Driven Microservices with Apache Kafka

Use when:

- Large number of microserivces that need to communicate async
- Require decoupled, fungible, independently maintained services
- One or more services produces events that are processed by multiple services
- You need a pattern that is more decoupled than standard HTTPS approach

#### Pros / Cons

**Pros**

- Decoupled, fungible and independent
- Messages are buffered and consumed when resources are available
- Services scale easily for high-volume event processing
- Kafka is highly available and fault-tolerant
- Conusmers / producers can be implemented in many different languages

**Cons**

- Introduces complexity and requires Kafka knowledge
- Handling partial failures can be challenging

### Kafka architecture

Uses Apache Kafka on Heroku to coordinate asynchronous communication between microservices. Here, services publish events to Kafka while downstream services react to those events instead of being called directly. In this fashion, event-producing services are decoupled from event-consuming services. The result is an architecture with services that are scalable, independent of each other, and fungible.
![alt text](https://devcenter0.assets.heroku.com/article-images/1544190561-kafka2.png)

### twelve-factor app

The twelve-factor app is a methodology for building software-as-a-service apps that:

- Use declarative formats for setup automation, to minimize time and cost for new developers joining the project;
- Have a clean contract with the underlying operating system, offering maximum portability between execution environments;
- Are suitable for deployment on modern cloud platforms, obviating the need for servers and systems administration;
- Minimize divergence between development and production, enabling continuous deployment for maximum agility;
- And can scale up without significant changes to tooling, architecture, or development practices.

1. **Codebase** - only 1 per app, same across all deploys
2. **Dependencies** - declares all dependencies, never relies on existence of system-wide packages or tools, simplifies dev setup
3. **Config** - store all config in environment, strict separation of config from code
4. **Backing servies** - treat backing services as attached resources, should be hot swappable without any code changes e.g switching db if it fails
5. **build, release, run** - strictly separate stages, ability to rollback, every release should have an ID, release ledger is append only
6. **Processes** - execute the app as one or more stateless processes, any persistence is stored in a stateful backing service
7. **Port binding** - Export services via a port binding
8. **Concurrency** - scaling via the process model, each type of work handled by a process type
9. **Disposability** - maximize robustness with fast startup and graceful shutdown
10. **Dev/prod parity** - keep dev, staging, production as similar as possible, small time gap - frequent deploys, small personnel gap - devs do deploys, small tool gap - dev and prod as simlar as possible
11. **Logs** - treat logs as event streams, each process writes logs to stdout - collated by log routers such as Logplex
12. **Admin processes** - run admin/management tasks as one-off processes e.g. running db migration, running console, running one-off scripts

### Erosion resistance

Erosion - operating system updates, kernel patches Apache, mySQL, ssh, OpenSSL security updates, server disk getting full, apps crashing or getting stuck, failure of underlying hardware
**Heroku is erosion-resistant**
dyno manager keeps dynos running automatically

## Integrating Salesforce and Heroku

| Technique                                    | Description                                                                                                         | When to use                                                                                                                                         |
| -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Heroku Connect                               | Bidirectional sync between SF and Heroku postgres                                                                   | unidirectional or bidirectional syncing (with eventual consistency guarantees) - **data replication and data proxy**                                |
| Salesforce Platform Events                   | event-bus integration that allows Heroku apps to create events on SF and subscribe to events published by SF        | Want a Heroku app with modern, high throughput, low-latency and integration with Salesforce enterprise messaging platform                           |
| Apex and Workflow callouts                   | call Heroku app API from Apex workflow or triggers                                                                  | want REST API integration based on object updates in SF                                                                                             |
| SF REST API                                  | Make calls to the SF REST API from within Heroku app                                                                | Need more than just data on SF org, for example want to programmatically go through an approval process - **data proxy and custom user interfaces** |
| Heroku External Objects & Salesforce Connect | expose data via Connect within SF org, to view, search on, and relate data to other objects                         | contextually reference data residing in Postgres from SF org, without having to physically copy data - **data proxy**                               |
| MuleSoft                                     | iPaaS and marketplace for integrating enterprise systems through their APIs, designing APIs and manage API services | use a managed platform for building disparate API integrations                                                                                      |
| CRM Analytics                                | Connects data inside and outside of SF with CRM Analytics for analytics                                             | Sync Heroku Postgres data to CRM Analytics                                                                                                          |
| Marketing Connector                          | attach as an add-on to sync marketing data                                                                          | Sync Heroku data with Marketing Cloud                                                                                                               |
| Slack                                        | A custom Slack app deployed on Heroku                                                                               | Integrate Slack with SF org or relay messages in event-based architectures                                                                          |
| Salesforce Data Cloud                        | data platform that enables customers to organize and unify data across SF and other external data sources           | Sync Heroku Postgres data with your Salesforce Data Cloud instance to create unified customer profiles and act on your data                         |

### Integration Suggestions

- To **replicate data** between SF and Heroku, use **Heroku Connect**
- to **expose Heroku Postgres data to SF**, use **Heroku Connect External Object**
- to **proxy** **OData**, **SOAP**, **XML**, or **JSON** data sources _into SF_, use **SF Connect**
- If Heroku connect not suitable, e.g. have **custom UI on Heroku** where users login via SF, use **SF REST APIs**
- To **offload** or **extend the processing of SF data events**, use **callouts** from SF to Heroku

## SF REST API

- nforce or JSForce libraries for Node.js provide wrapper for invoking the API
- SOQL queries
- OAuth for authentication

## Apex and Workflow callouts to your Heroku API

- Used for when you want REST API integration based on object updates in SF
- Apex HTTP callouts for making REST calls
- Workflow outbound messages for making SOAP calls - configure SF to automatically send messages to external endpoint e.g. Heroku app, whenever specified fields change
- Calling from SF to Heroku tightly couples the two, recommended to use Salesforce Platform Events

## Terminology

[glossary of heroku terminology](https://devcenter.heroku.com/articles/glossary-of-heroku-terminology)

## Quiz questions

| Question                                                                                             | Answer                                                                                                                                                                                                                            |
| ---------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| In the hierarchy of cloud services, PaaS generally provides more out of the box than:                | Infrastructure as a service (IaaS) and on-premises infrastructure                                                                                                                                                                 |
| Which of the following is a good use case for the Heroku Platform?                                   | Customer engagement applications **AND** Empowering mobile apps with an API service **AND** Data manipulation **AND** Proof-of-concept or lab approaches                                                                          |
| Which deployment option allows you to automate deployments?                                          | GitHub integration                                                                                                                                                                                                                |
| What is a pro of using a 'Deploy to Heroku' button?                                                  | You can add the button to a repo’s README file.                                                                                                                                                                                   |
| What is a Heroku dyno?                                                                               | A managed runtime container with a Linux operating system underneath                                                                                                                                                              |
| How are languages, buildpacks, and slugs related?                                                    | A buildpack knows how to compile code in a specific language down to a slug that runs on Heroku.                                                                                                                                  |
| Heroku Connect is an add-on that syncs SF data into:                                                 | A Heroku Postgres database                                                                                                                                                                                                        |
| The Private Spaces feature can be useful if you need to:                                             | Ensure that your application's incoming traffic originates from a allowlisted set of IP addresses **AND** Speed up an application's response time by running it on dynos that are located geographically closer to your customers |
| What characterizes continuous integration as a development process?                                  | Including and integrating every code change on every commit.                                                                                                                                                                      |
| What are some benefits of continuous delivery?                                                       | You push code to a production-like environment (staging). **AND** You can promote from staging to production when you’re ready.                                                                                                   |
| Which process is part of a common continuous delivery workflow on Heroku?                            | A review app spins up when creating a pull request in GitHub. **AND** Heroku CI runs tests in a disposable app.                                                                                                                   |
| What's one thing you can do with Release Phase?                                                      | Upload assets to CDN or invalidate a cache.                                                                                                                                                                                       |
| Which of the following options are available for Heroku Pipelines if you use the GitHub integration? | Automatic deploys to staging. **AND** Automatic test runs with each code change with Heroku CI.                                                                                                                                   |
| What is the best way to preview code changes in a browser window?                                    | Click "Open app in browser" next to the review app                                                                                                                                                                                |
| How do you set up Release Phase?                                                                     | Open the Procfile and add a release process type and command.                                                                                                                                                                     |
| Where can you find release logs?                                                                     | In the Activity tab of your app.                                                                                                                                                                                                  |
| You can use Salesforce Connect to proxy which types of data sources:                                 | OData 2.0 and 4.0 **AND** REST with JSON payloads **AND** REST with XML payloads **AND** SOAP                                                                                                                                     |
| Callouts from Salesforce to Heroku can be made using:                                                | Apex triggers or outbound messages                                                                                                                                                                                                |
| You can use Heroku Connect for:                                                                      | One-way data replication **AND** Bidirectional data replication **AND** Data proxy with Salesforce Connect                                                                                                                        |
| Heroku Connect data replication happens:                                                             | Near real time either by poll on-demand in response to changes or via a specified interval                                                                                                                                        |
| Salesforce Connect is used for                                                                       | Proxying external data into SF                                                                                                                                                                                                    |
| Salesforce Connect custom adapters support                                                           | Cross-object relationships **AND** Data paging **AND** Search                                                                                                                                                                     |
| Applications on Heroku that use SF REST APIs can use which auth mechanisms:                          | Username and password with an OAuth Connected app **AND** OAuth web or user-agent flow in which the user authorises a connected app using browser redirects                                                                       |
| You can use SF REST APIs to:                                                                         | Insert or update records                                                                                                                                                                                                          |
| The primary benefit of a workflow outbound message over an Apex trigger is:                          | Messages are completely declarative.                                                                                                                                                                                              |
| Heroku apps that handle callouts from Salesforce can be written in:                                  | Node.js / JS, Java, Scala Clojure, Python, PHP (any language that Heroku supports)                                                                                                                                                |
