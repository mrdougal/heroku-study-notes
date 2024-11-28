# Add-ons with private spaces

Some add-ons "available" with private spaces, may route traffic over public internet

Add-ons marked as "available and installable" with private spaces means traffic stays within private network

- PG need to select `private-0` plan
- KVS

## Trusted IP addresses

Private spaces come with a set of trusted IP addresses
Only clients from these address can access web processes running in the private space.

Note there is no limit to access to dashboard, databases or execute CLI commands

IP ranges for data services, aka PG or Kafka are not available in private spaces (beta feature)

### Availability

Min 3 dynos for each process type.
Dyno's provisioned (round robin) in one of the three availability zones. (AZ)

Availability zone, discrete data-center with redundant power/networking/connectivity in an AWS region.
Traffic between AZ's encrypted. Physically separated ~100km from each other
