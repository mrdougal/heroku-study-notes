Can you share a PG addon between applications?

Can you have multiple VPN connections to a private space?
**No** but you can have 2 tunnels.

Connecting to Google Cloud or Azure, can you use peering or does it have to be VPN connection? \*_yes_ as peering is a connection within a cloud providers network
(not on fir)

## Heroku connect and encrypted fields, how does connect handle that data?

Heroku connect, wanting to write two rows of related objects from salesforce. What limit on `external_ids` is there?

- Do you need to have them on the parent object?
- Slows down due to salesforce having to supply the id?

Dataclips on PG? - read only **yes**

Web application in Ruby, Java worker/

- ~~Apps in `git submodules` and set types in procfile~~ submodules don't work
- Use two buildpacks

Logs sent to multiple providers? **yes** send logs to Logplex then on from there

Logging in private space. **yes** provided endpoints are via SSL, `syslog drains` aren't supported though.

Heroku "data blocks" - The infrastructure for private/shield data services
Isolated, dedicated compute and storage

- PG
- Redis
- Kafka

## PG follow, vs fork
