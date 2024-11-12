# Integration

With salesforce

[Connect](./enterprise/connect.md) and by extension can reference objects from Salesforce

- Salesforce Platform events

## Apex/Workflow api calls

- Apex calls "Apex callouts" calls your API (typically POST to a REST endpoint with JSON)
- [Outbound messaging](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_om_outboundmessaging.htm)
  - Fields in salesforce triggers messages with field values. (part of workflow rule)
  - "Workflow outbound messages" making SOAP calls

## Calling salesforce REST api

create, manipulate and search data

## Salesforce data cloud

via ~~several~~ many data connectors

- CRM Analytics
- "Marketing connector" from "Softtrends"
- Slack

## Mulesoft

> "Integration platform as a service"

- Service for integrating other services, can call these API's from your heroku app
- Can use any `JDBC` compliant database including Kafka

If in private or shield need a bit more configuration for additional security

---

## General suggested services

- Replicate data between Salesforce and Heroku, use **Heroku Connect**.
- To expose a Heroku Postgres database to Salesforce, use **Heroku Connect External Object**.
- To proxy `OData`, `SOAP`, `XML`, or `JSON` data sources into Salesforce, use **Salesforce Connect**.
  - If Heroku Connect doesn't fit, eg: you have a custom UI on Heroku, use the **Salesforce REST APIs**.
- To offload or extend the processing of Salesforce data events, use **callouts** from Salesforce to Heroku.
