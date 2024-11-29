# Apex/Workflow api calls

- Apex calls "Apex callouts" calls your API
  - can be `SOAP` call sending `XML`. Typically require `WSDL` (web service description language. XML) for code generation
  - can be `REST` with `JSON`

Can use either but REST is recommended as it's less code and easier to interact with. SOAP is considered legacy but still relevant

In Salesforce, need to setup API's that Apex can call (REST and SOAP)

## Mocking callouts

Apex test methods will fail if there are callouts to an api that's not mocked

## With SOAP

use `WSDL2Apex` to generate code

## Setup Apex class as a web service

Can be REST or SOAP web service.

With `REST`

- Define `class as a global` and it's methods as `global static`
- `@RestResource` defines base endpoint if it's rest
- Need to annotate the class with the endpoint methods
- recommend to version endpoints
- Supports OAuth 2
- update is a PUT to `/services/apexrest/Class/` not `/services/apexrest/Class/<RecordID>` (the `id` needs to be in the payload)

- If a package has endpoints they're namespaced `/services/apexrest/packageNamespace/method`

With `SOAP`

- Define `class as a global` and it's methods as `global static`
- add `webservice` keyword
- generate `WSDL` from "class details page"
- auth required `Enterprise WSDL` or `Partner WSDL`

- **Can in theory use SalesforceREST or SOAP API's but with Apex you can write your own custom logic**
- **Apex web services operate with system privileges** vs Salesforce API which respects users object and field permissions
