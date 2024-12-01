## Problem-space

- Building point to point API integrations timely and can be brittle.
- Lots of custom logic between API's
- End up with tightly coupled systems

## API-led connectivity (Mulesoft)

- Clear contracts between systems
- Reusable
- Discoverable
- Security
- Resiliency

- Offer tools to design, build, deploy and operate "an application network"

## Categories of API

- **System**, data from core assets eg: db's
  - **"Anypoint platform"** tools to create and manage API's
  - typically then aggregate into "process api's"
- **Process**, shape data for business needs
  - **Mulesoft Studio**
  - examples customer orders, order fullfillment
- **Experience**, shape for consumption
  - **Mulesoft composer** can build api's with clicks
  - mobile app API

## Design an API spec first

- behaviours
- data types
- serves as doc for future
- can preform acceptance tests
- can be used to produce test data

[`RAML`](https://raml.org/) "restful api modelling language" native spec in Mulesoft, but can import OAS/Swagger.
(looks a lot like yaml and OpenAPI)

Publish to API exchange
Once in exchange and API's are tagged can "govern" them by applying "rulesets" - helps with conformance
