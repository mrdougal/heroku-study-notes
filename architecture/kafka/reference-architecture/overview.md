# Even driven microservices

Lots of microservices

- decoupled
- fungible (can be replaced with like for like)
- independently maintained. Different teams
- events to be processed by many services

Services publish events to Kafka
Downstream services react to those events (vs being called directly)

Kafka high availability as it retains messages/events (configurable)
Can rewind and replay events

## Client config

- Needs at least one broker
- Needs a `clientId`

- Don't be afraid to mix protocols. As sometimes `https` makes more sense. (eg: handle image uploads)
