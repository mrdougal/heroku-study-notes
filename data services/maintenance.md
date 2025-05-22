Via an addon you can review maintenance windows for data ons. (PG and Redis)

```bash
heroku plugins:install @heroku-cli/plugin-data-maintenance
```

- Review windows
- Change
- Schedule maintenance event
  - Reschedule an event
- View details of planned event
- History
- Wait for trigger event to complete
- Test impact on a staging app

For Kafka there's less information available. Only `info` and `history`
