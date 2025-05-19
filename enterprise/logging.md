## Audit trails

[See also logging](../security/security.md#logging)

- Heroku provides a separate event archive for each calendar month.
- It does not provide real-time event logging
- Need to have `manage` access

### Needs `plugin-enterprise`

```bash
heroku plugins:install @heroku-cli/plugin-enterprise
```

Can then export events for a particular month

```bash
heroku enterprise:audits:export 2018-01 -e my-enterprise-account-name
```

Note that the events in the archive are **not guaranteed to be in chronological order**

### Events

The following events are audited

- "addon.attach"
- "addon.create"
- "addon.destroy"
- "addon.detach"
- "addon.update"
- "app.create"
- "app.destroy"
- "app.update"
- "app_transfer.create"
- "app_transfer.update"
- "code_release.create"
- "collaborator.create"
- "collaborator.destroy"
- "config_change.remove"
- "config_change.set"
- "domain.create"
- "domain.destroy"
- "enterprise_account_membership.create"
- "enterprise_account_membership.destroy"
- "enterprise_account_membership.update"
- "heroku_config_change.update"
- "sni_endpoint.create"
- "sni_endpoint.destroy"
- "sni_endpoint.update"
- "space.create"
- "space.destroy"
- "space.update"
- "team_membership.create"
- "team_membership.destroy"
- "team_membership.update"
- "team.destroy"
- "team.update"
- "trusted_ip.update"
