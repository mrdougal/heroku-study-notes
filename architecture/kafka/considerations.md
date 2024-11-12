# Considerations

- Message order

Messages in a partition are ordered, but this isn't guaranteed across partitions

- Consumers in parallel

Resource utilization
Lots of partitions = more memory and time to recover or re-elect leaders

---

## Order of events

Is order of events isn't important, could
Is it important to have your data in order?

[Pros/Cons](https://www.altexsoft.com/blog/apache-kafka-pros-cons/)

> when your daily loads of data are small (say, no more than several thousand messages), it’s more reasonable to stay with traditional message queues.
