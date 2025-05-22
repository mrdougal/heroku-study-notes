# Heroku - Docker

https://devcenter.heroku.com/articles/container-registry-and-runtime

Is an advanced use case. As updates to base image aren’t supported.

Common runtime and private spaces supported
Need to set stack is set to `container`
`heroku stack:set container`

## Container registry

They run their own registry
`heroku container:login`

Build image and push to registry
`heroku container:push web`

Release the image
`heroku container:release web`

## `heroku.yml`

Has 4 top level sections

- `setup` define add-ons and config vars
- `build` which `Dockerfile` to build
- `release` specifies release phase tasks
- `run` process types and commands to run

```yaml
setup:
  addons:
    - plan: heroku-postgresql
  config:
    S3_BUCKET: example-bucket
  build:
    docker:
      web: Dockerfile
      worker: worker/Dockerfile
  release:
    command:
      - ./development.sh
    image: worker
  run:
    web: bundle exec bin/start
    worker: python worker.py
```

## Gotchas

Can’t use heroku CI to test container builds
If you want to stream logs, your image needs to have `curl`

Docker images run in same way as slugs

- Web process must listen to http traffic
  - On `$PORT` set by heroku
  - Only http supported
  - Can set different directory via `WORKDIR`
