# Zookeeper

> For metadata management of Kafka clusters

Heroku doesn't allow it. _Why?_

Is being deprecated.
Not recommended for new deployments
Will be removed in `v4.0` of Kafka

Being deprecated because

- wasn't made for Kafka so there were some workarounds
- added complexity with architecture as needed an additional machine

Being replaced by "KRaft" (brokers in `KRaft` mode)
https://kafka.apache.org/documentation/#kraft_zk_migration
