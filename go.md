# GUIDELINES FOR GO PROJECTS

## Vendoring
- go modules (be careful with go proxy for private projects)
    - go mod init
    - go mod vendor
    - go mod tidy
    - go get

## Program Perfomance
- benchmark/loadtest everything we release
- use pprof handler to profile on demand
- instrument it to measure latency, throughput, idle-time (counters, gauges etc)
- instruments satisfy the http.Handler interface

## Observability
- expose realtime measurements at /statz endpoint, using instruments handlers
- listen on localhost socket for safety
- exporter would push statz to TimeSeries DB (prometheus?) or publish to topic <workload>.statz (NATS?)

## Controle plane
- fetch /statz for the workloads
- or subscribe to <workload>.statz to get messages (NATS ?)

## Context
- pkg context
- store info related to requests that need to be propagated (jwt,traceID,t0 etc)

## Repo architecture
- cmd/ holds a folder for each program (main.go): as small as possible
- a single program per repo isolates releases + shorter build times
- library repos do not have cmd
- program repo must move packages in /pkg if more than 3/5
- internal/ holds packages that can not be imported

## Layered architecture
- provides separation of concerns and dependency injection
- domain layer: types, const, entities, aggregators, validators + data manipulation
- app layer: the business logic => features, depends on abstractions (interfaces)
- infrastructure layer: implementation of the interfaces (dependencies on underlying technologies or external services)
- mock as much as possible with predictable behaviour (configurable latency, random failures etc)

## Continuous Integration
- format, lint, test, build for each push
- simple `docker build Dockerfile` with code mounted in a volume

## Deployment
- never more than one release late in prod !!!
- at least one release per sprint
- agility = release->prod as short as possible

## Profiling
- go tool pprof
- go tool trace
