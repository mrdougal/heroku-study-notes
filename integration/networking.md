# Networking terms

## Peering

- Connection between two VPC's
- Can route traffic between the VPC's with IPv4 or v6 addresses
- Instances in either VPC can communicate as if on same network
- ~~Can be between cloud providers~~ (not so sure, out of the box definitely AWS)
- Can span regions (inter-region VPC peering connection)
- Connection is at VPC level, resources can use private ip addresses to access resources.
- Traffic remains in the private IP address space
- With AWS it never traverses the public internet

## VPN tunnel

If you need to cross cloud providers, eg: AWS to Azure

## CIDR (Classless Inter-Domain Routing)

Response to classful address. IPv4 address
As allocation of addresses is inefficient.

- Class A supported `16,777,214` hosts
- Class B supported `65,534` hosts
- Class C supported `254` hosts

If an org has 300 devices they need to apply for Class B

### Benefits

- quicker to route packets
- more efficient use of available IP addresses
