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

## Limitations

- only dynos of `shield` type can run. (they have an encrypted file system)
- only `shield` pg plans permitted
- only `shield` "key value store" plans. Have strict connection requirements
- only `shield` heroku connect plan
- only `shield` kafka plan. "Allow streaming of certain regulated data classes" that can't be store in `private` plans
