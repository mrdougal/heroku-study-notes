When a db is provisioned data is store in region it's created.

- You can provision data services outside of your region although they won't be automatically networked to your space. `--region=us`

Some services for managing your DB may exist outside of your region.

- PGBackup snapshots are stored in US
- Dataclips are in the US
- Log data (Logplex is hosted in US)
  - can block on creation via `--block-logs`
  - Shield logging doesn't use Logplex
    Heroku API and build are global services and not associated with a region

# List of regions
