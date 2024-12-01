# https://www.odata.org/

> Best practices for building and consuming RESTful API's

Dictates how data services interact with clients over HTTP. (Similar to OpenAPI)

- GET, Create etc
- Search via query string (defines convention for params)
- Invoking functions via the url. (sounds like a terrible idea)
- Versioning via `OData-Version` header
  - Clients also include `OData-MaxVersion` header

Use `OData` with salesforce connect
