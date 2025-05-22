# Heroku - Kafka (event streaming)

https://kafka.apache.org/

“Cluster” of “brokers”/instances running Kafka. Number of brokers can be scaled up/down

## “Producers” write to brokers

Applications that "publish" events

## “Consumers” read from brokers

Producers and consumers are fully decoupled.

## “Brokers”

Manage streams of messages/events in “topics”

## “Topics“

- Storage of events.
- Like a folder in a filesystem.
- Topics always multi-producer and multi-subscriber
- Events in a topic can be read many times. Not deleted after consumption - can be configured. Performance with saving large datasets ok.

**Consumers pull information, vs having it pushed to them**

### Topics are partitioned

- Spread over number of “buckets” located on different brokers. (for scale)
- Can be replicated across geo-regions or data-centers
- in a partition the events/messages are in chronological order

Take events route to “partitions”

## Guarantees

Events processed only once
Events read in the order they were written

## Shape of an event

"something that happened"

- `key`
- `value`
- `timestamp`
- `metadata` headers - optional

---

- Process events into data pipelines
  - Pub/sub
- Can replicate messages for durability if a “broker” fails.
  - (Stores messages) for a while or indefinitely

Used for

- Elastic queuing
  - Buffer of real time messages
- Data pipelines
  - ETL (extract transform load) get “value” from data
  - Event stream is immutable so can build parallel processing
- Microservices co-ordination
  - Easier to bootstrap a service into network
  - Reduce ordering and dependences

## CQRS Command Query Response Segregation

> separating the write model from the read model

INCOMING -> command -> events -(alter)-> state -(reads)-> query -> ANSWER
