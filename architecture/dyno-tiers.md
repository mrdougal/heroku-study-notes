# Heroku - Dyno Tiers

| Plan                             | Memory (RAM) | CPU Share | Max Number of Threads | Compute | Sleeps |
| -------------------------------- | ------------ | --------- | --------------------- | ------- | ------ |
| Eco                              | 512 MB       | 1x        | 256                   | 1x-4x   | ✔      |
| Basic                            | 512 MB       | 1x        | 256                   | 1x-4x   |        |
| Standard-1X                      | 512 MB       | 1x        | 256                   | 1x-4x   |        |
| Standard-2X                      | 1 GB         | 2x        | 512                   | 2x-8x   |        |
| Private/Shield-S                 | 1 GB         | 100%      | 512                   | 12x     |        |
| Performance/Private/Shield-M     | 2.5 GB       | 100%      | 16,384                | 12x     |        |
| Performance/Private/Shield-L     | 14 GB        | 100%      | 32,768                | 50x     |        |
| Performance/Private/Shield-L-RAM | 30 GB        | 100%      | 24,576                | 24x     |        |
| Performance/Private/Shield-XL    | 62 GB        | 100%      | 24,576                | 50x     |        |

## Eco

- 512MB RAM, 1 CPU
- $5 a month
- Monthly pool of hours
- Only one size dyno “Eco”x`
- Hours shared by all dynos
- Dyno’s sleep after inactivity
- Only 1 dyno per process type
- Good for prototypes

## Basic

- 512MB RAM, 1 CPU
- One one size dyno “Basic”
- Good for small projects
- No pre-boot
- No scaling
- Only 1 dyno per process type

## Standard

- For more robust apps
- Multiple sizes of dynos
  - Standard-1X 1GB RAM 1 CPU
  - Standard-2x 1GB RAM, 2 CPU’s
- Can pre-boot
- Can auto-scale

## Performance

_Limited to customers with established payment history_

- All features of Standard…
- Single tenant
- High compute and memory

Apps can use a mix of standard and performance. eg `web` is on performance and `worker` is on standard
