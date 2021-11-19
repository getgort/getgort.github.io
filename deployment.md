# Deploying Gort

This guide will instruct you through the process of installing the Gort controller.

The Gort controller is the core Gort service, which relays messages to and from the chat providers, manages relay command instructions, and exposes the REST administration API. See [Gort Architecture](architecture.md) for more information.

## Running Gort in Kubernetes

If you have an existing Kubernetes cluster (or are using something like Minikube or Docker Desktop Kubernetes). you can deploy Gort there. The easiest way to do this is to use the included Helm chart.

### Prerequisites

You'll need to have [Helm](https://helm.sh/) installed. If you don't, you can follow the [instructions here](https://helm.sh/docs/intro/install/).

### Deployment

Installing the Helm chart is fairly straight-forward:

```bash
helm install gort helm/gort
```

## Running Gort in Docker

If you don't have Go installed, you can run Gort with Docker. 

### Building Your Own Gort Image (Optional)

If you want to use the most absolutely bleeding-edge version of Gort, you can build your own local Gort image. If you don't mind using the stable version, you can skip this step and Docker will automatically download it for you.

To build your own local image you can use the `make` file included in the root of the Gort repository:

```bash
make image
```

You can verify that this was successful by using the `docker image ls` command:

```bash
$ docker image ls
REPOSITORY     TAG       IMAGE ID       CREATED         SIZE
getgort/gort   0.9.0     66fca0b90847   5 seconds ago   109MB
getgort/gort   latest    66fca0b90847   5 seconds ago   109MB
```

This should indicate the presence of two images (actually, one image tagged twice) named `getgort/gort`.

Now you can check the version of your installation by passing it the `version` command (which is equivalent to executing `gort version`).

```
$ docker run getgort/gort version
Gort ChatOps Engine v0.9.0
```

### Starting Containerized Gort

Finally, from the root of the Gort repository, you can start Gort by using `docker compose` as follows:

```bash
docker compose up
```

If everything works as intended, you will now be running three containers: 

1. Gort (listening on port 4000)
2. Postgres (a database, to store user and bundle data)
3. Jaeger (for storing trace telemetry)

Finally, you should see some output similar to the following:

```
INFO  [0000] Loaded configuration file               file=config.yml
INFO  [0000] Starting Gort                           version=0.8.0-alpha.0
INFO  [0000] Installing adapter                      adapter.name=Gort
INFO  [0001] Connection to data source established   type=postgres.PostgresDataAccess
INFO  [0001] Gort controller is starting             address=":4000"
INFO  [0001] Connecting to Slack provider            adapter=Gort provider=Gort
INFO  [0001] Connection established to provider      adapter.name=Gort adapter.type=slack event=connected
```

As you may have noticed, this command opens port 4000. This allows the Gort controller to access the administration API, which is required to bootstrap your Gort instance.

Congratulations, you now have a running Gort controller!

## Running Gort as a Native Gort Binary

### Installing Gort via `go install`

If you have [Go installed](https://golang.org/doc/install), you can build and install Gort in one command using the `go install` command.

```
go install github.com/getgort/gort@latest
```

When installed this way, Gort will be installed to the directory named by the `GOBIN` environment variable, which defaults to `$GOPATH/bin` or `$HOME/go/bin` if the `GOPATH` environment variable is not set.

### Building Gort From Source

If you prefer (if you have [Go installed](https://golang.org/doc/install)), you can also build Gort from the source code.

To do this, you must first clone the [getgort/gort](https://github.com/getgort/gort) repository and `cd` into it.

```
git clone git@github.com:getgort/gort.git
cd gort
```

Once you're in the Gort code repository, you can use `go build` to build the Gort binary.

```
go build
```

You should now have an executable binary named `gort` in your working directory. You can either run it in place, or move it a directory on your command path.

### Executing a Native Binary

If you installed or built Gort using `go`, you can run that binary, pointing to the location of the configuration file.

```
gort --config ./config.yml
```
