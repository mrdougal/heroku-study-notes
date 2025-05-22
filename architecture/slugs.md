# Slugs

- Timeout **15mins to build**
- Max allowed size is 500Mb (after compression)

Can inspect slug via heroku run bash” and use commands like “ls” or “du” (disk usage)

`.slugignore`
Files to exclude from build. Format is like `.gitignore`

- Psd
- Pdf

Files are removed before the build pack runs.

## Reducing slug size

- Move assets to external store
- Purge build cache
  - Cached dependencies, to speed up future builds

To empty cache
`heroku plugins:install heroku-builds`
`heroku builds:cache:purge`
