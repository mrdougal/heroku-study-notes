# Heroku - Kafka (event streaming)

[Kafka](https://kafka.apache.org/)

“Cluster” of “brokers”/instances running Kafka. Number of brokers can be scaled up/down

- `Producers` write to brokers
- `Consumers` read from brokers
- `Brokers` manage streams of messages/events in “topics”
- `Topics` configured with range of options into “partitions” or “buckets” located on different brokers
- `Partitions` subsets of a topic. (used to balance concerns of parallelism). Messages within a partition are ordered, but messages across partitions aren't guaranteed to be in order.
  - Balancing act of needs of parallelism vs ordering of messages
  - Each message in a partition has an `offset` to denote it's position in the queue

Take events route to `partitions`

- Process events into data pipelines
  - Pub/sub
- Can replicate messages for durability if a `broker` fails.
  - (Stores messages) for a while or indefinitely

## Used for

- Elastic queuing
  - Buffer of real time messages
- Data pipelines
  - ETL (extract transform load) get “value” from data
  - Event stream is immutable so can build parallel processing
- Microservices co-ordination
  - Easier to bootstrap a service into network
  - Reduce ordering and dependences

—

Install

`heroku plugins:install heroku-kafka`

- Requires python
- Running on Docker good way to run locally
  - Give small memory to allow for comfortable operation of local machine
- On private spaces recommend against using [zookeeper](https://zookeeper.apache.org)
