# Service Kit

Your swiss army knife for microservices in Go.

## Motivation and goals
The goal of this library is to provide comprehensive toolkit over basic Go primitives and idioms and thus enable creation of complex backends designed as microservices.

## Library design
Library provides abstraction for many functional areas and technologies commonly used for backend creation, e.g.:  

* metrics & health-checks support
* logging
* messaging
* service mesh
* common patterns like fault tolerance (leader/follower), saga, etc.

## How to get started
Before getting really started familiarize yourself with following core building blocks:

* [goreman](https://github.com/mattn/goreman) process manager 
* [nsq](https://nsq.io/) realtime messaging
* [consul](https://www.consul.io/) service mesh

Once you have some basic idea, have a look at [example services](https://github.com/minus5/examples-services) utilizing these technologies. To see even more follow the [Examples](#examples) section below.

## Examples
Practical usage of the library can be found [here](./example).

## Modules

svckit contains 43 independently consumable modules, developed together through
[go.work](./go.work). Package directories, import paths, APIs, and the Go 1.25
baseline are preserved. Each module declares its dependencies.

Main functional areas have their own modules. Related packages stay together:
`amp/broker`, `amp/session`, and `amp/ws` belong to `amp`; `dcy/sr` and `dcy/lazy`
belong to `dcy`. Optional adapters and tools have separate modules: `amp/gen`,
`amp/nsq`, `metric/conn`, `metric/dev`, `metric/prometheus`, and `metric/statsd`.
Libraries under `pkg/` retain their current paths. Mongo test helpers are
separate from `pkg/testu` and `pkg/testu2`; `pkg/jsonu/frontend`,
`pkg/util/profile`, and `pkg/util/mongo/api` are separate from their parents.

The root `github.com/minus5/svckit` remains a compatibility umbrella requiring
every child module. Commands and examples remain at the root.
[modules.go](./modules.go) retains those requirements during `go mod tidy`,
using a build tag that excludes its imports from normal builds. Applications
needing only a small library should require that child directly: `env`, `file`,
`metric`, `pkg/defaults`, and `saga` have no third-party requirements.

### Working locally

Work inside the module you want to change, using normal Go commands:

```sh
cd jsonreq
go build ./...
go test ./...
```

`go.work` lists the modules and resolves local siblings. Root `go test ./...`
does not traverse nested modules; CI runs Go commands in each workspace module.
Version-specific replacements in `go.work` resolve the initially unpublished
sibling versions locally. No local replacements are committed in `go.mod`.

### Consumer upgrades and release

The manifests prepare an initial `v0.1.0` release. These versions are not yet
published. After publication, existing applications can keep their imports:

```sh
go get github.com/minus5/svckit@v0.1.0
go mod tidy
```

Their `go.mod` and `go.sum` gain child requirements and checksums. Applications
pinned to earlier monolithic revisions continue using those revisions.
Combining an old root with a new child can cause an ambiguous import; upgrade
the root in that dependency graph first. New applications can require a child
directly, for example `go get github.com/minus5/svckit/metric@v0.1.0`.

For local development alongside an application, add the required modules to its
workspace or replace them individually. Replacing only the root does not
replace its children.
