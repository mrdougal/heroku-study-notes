### Connecting

DB's can't be shared between spaces, common runtime or anywhere else
Connections can only be made from dynos in the same space or

- Heroku Connection
- AWS private link (a tunnel across AWS) Must be in same subnet, heroku notifies people it's active
- Mutual TLS, good if need to connect outside of AWS
  - Need to also allowlist the IP connecting from (Hard limit of 20)
  - Only for PG
- Trusted IP's for data (beta feature)
  - not available in Shield
  - `0.0.0.0/0` is ignored
  - Is controlled by heroku need to email to change while in beta
