# Shield private spaces

https://devcenter.heroku.com/articles/shield-private-space

- High compliance
- Strict TLS enforcement
- Keystroke logging on `heroku run`
- Doesn’t use `logplex` (as entire space is logged)

## Limitations

- Only dynos of `shield` type can run. As filesystem is encrypted
- `shield` PG and KVS plans. Have strict connection requirement
- `shield` Kafka. Allow streamed of regulated data classes that isn't permitted in other plans
- CLI access to databases isn't allowed
