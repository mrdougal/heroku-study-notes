# Node

Runtime limited to…

- Single CPU core
- ~1.5Gb Memory

To go beyond these limits, cluster your app https://nodejs.org/api/cluster.html

- Forky for separate main and worker files
- Throng for single entrypoint (this is what Heroku uses)
  - https://github.com/hunterloftis/throng last commit was 4 years ago though…

Since node is single threaded, need to [fork processes to scale](https://devcenter.heroku.com/articles/node-concurrency)

Node’s V8 uses a lazy garbage collector, default limit of 1.5Gb so waits until has to cleanup. If memory usage is up, it’s [possibly because of node](https://github.com/nodejs/node/issues/3370#issuecomment-148108323)

Can provide flags in Procfile for Node

web: node --optimize_for_size --max_old_space_size=920 --gc_interval=100 server.js

Node specific hooks

- `heroku-prebuild` runs before install dependencies
- `heroku-postbuild` run after install dependencies, but before prunes and caches dependencies
- `heroku-cleanup` runs after hero prunes and caches dependencies

Private dependencies
Add private registry to `.npmrc`

@scope:registry=https://registry.npmjs.org
