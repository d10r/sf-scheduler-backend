# About

Minimal Superfluid [Vesting|Flow|Wrap]Scheduler executor with no runtime dependencies other than an RPC endpoint.

Setup:
```
yarn install
```

Run vesting schedules:
```
RPC=<url> PRIVKEY=<pk> node runVestingSchules.js
```

Run vesting schedules v2:
```
USE_V2=true RPC=<url> PRIVKEY=<pk> node runVestingSchules.js
```

Run flow schedules:
```
RPC=<url> PRIVKEY=<pk> node runFlowSchules.js
```

Run wrap schedules:
```
RPC=<url> PRIVKEY=<pk> node runWrapSchedule.js
```

For other (optional) env vars, check the source files.

## How it works
* initialize for the RPC defined network (using metadata)
* range query log events of the scheduler contract from latest processed block to latest block and update the local state accordingly
* execute what needs to be executed

The current state is written to a json file named `state_<network>.json`.

The process quits after having processed everything, it's designed to be periodically started, e.g. by a cronjob.
(For robustness, some mechanism to watch and restart on failure would be needed anyway)

You can query state files with jq, e.g.
```
cat data/vestingschedule_polygon-mainnet.json | jq '.activeSchedules[] | select(.started == false and .startDate < now)'
```
gives you the active vesting schedules for Polygon mainnet which are due to be started and weren't yet.

## What next?

The current implementation of doing transactions isn't ideal for batches of schedules due at the same time.
Should batch transactions, e.g. using Multicall3.

Writing to an sqlite DB would also be more elegant than just a json file. But in terms of scalability that becomes relevant only with much more usage.
