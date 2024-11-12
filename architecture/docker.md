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

## Gotchas

Can’t use heroku CI to test container builds
If you want to stream logs, your image needs to have `curl`

Docker images run in same way as slugs

- Web process must listen to http traffic
  - On `$PORT` set by heroku
  - Only http supported
  - Can set different directory via `WORKDIR`
