## PG

Premium, Private and Shield come with `HA` High Availability

If primary db fails

- is replaced by replica db. (the standby)
- primary destroyed
- new standby is built

Standby node is hidden from application.
If need followers for scale or reporting, create new "standard tier" follower db

HA standby are in different availability zone. (different physical location, but close by)

Failover will not occur if standby is more than 10 segments behind primary

## Redis

Similar to PG but replication is async, but since it's all in memory it's fast

## Kafka

Heroku distributes Kafka brokers across AZ's.
Not all AZ's are the same

Frankfurt, Sydney and Tokyo only have 2 AZ
Any plan used in these areas should be an `extended plan` with replication factor at 4 (has 8 brokers)
A `standard` plan has 3 brokers, so 2 will be in one AZ and the 3rd in the other AZ.  
So if there's a failure in first AZ there is only 1 broker to handle the load

**Private and Shield data services can be used in a Shield Private Space**
