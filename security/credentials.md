# Credentials

## PG

By default role is one step below super user
Can manage via data.heroku.com or CLI

```bash
heroku addons:attach postgresql-DB_NAME --credential CREDENTIAL -a APP_NAME
```

Can `detach` and `rotate` credentials for PG

## Redis

Cannot create new credentials for **Redis** need to reset `heroku redis:credentials  --reset`
New credentials are created for your instance, config vars for application are updated.

- Open connections remain open until tasks complete. (So app is not halted and left in inconsistent state)
- Any new connections opened after reset will fail if dyno's haven't been restarted

## Kafka

Similar with **Kafka** `heroku kafka:credentials --reset`
When reset is run...

1. New credential generate for cluster
2. Existing Topics/Consumer groups get new ACLs (who produces/consumes topics)
3. Config vars are updated on application
4. For `5mins` old/new certificates are valid

# Connect

Credentials via OAuth token that can be revoked at any time
