# Getting Started

This guide will instruct you through the process of installing the Gort controller.

The Gort controller is the core Gort service, which relays messages to and from the chat providers, manages relay command instructions, and exposes the REST administration API. See [Gort Architecture](architecture.md) for more information.

## 1. Prerequisites

To install Gort, you'll need the following:

- [Go compiler](https://golang.org/doc/install) (if using `go install` or `go build`)
- [Docker](https://docs.docker.com/get-docker/)

## 2. Installing Gort

Like most software, before you run the Gort controller, you have to install it. You have three options for exactly how you want to do that.

### a. Installing Gort via `go install`

If you have [Go installed](https://golang.org/doc/install), you can build and install Gort in one command using the `go install` command.

```
go install github.com/getgort/gort@latest
```

When installed this way, Gort will be are installed to the directory named by the `GOBIN` environment variable, which defaults to `$GOPATH/bin` or `$HOME/go/bin` if the `GOPATH` environment variable is not set. 

### Building Gort from source

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

### Containerized Gort

If you don't have Go installed, you can run Gort with Docker. 

The installation is straight-forward. To install the latest version:

```
docker pull getgort/gort
```

Now you can check the version of your installation by passing it the `version` command (which is equivalent to executing `gort version`).

```
$ docker run getgort/gort version
Gort ChatOps Engine v0.8.0-alpha.0
```


## Running Gort

To run the Gort binary you'll need the following:

- A Docker daemon running (for executing bundled commands)
- A Gort configuration file

If you're not running in development mode, you'll also need access to a Postgres database.

### Executing a native binary

If you installed or built Gort using `go`, you can run that binary, pointing to the location of the configuration file.

```
gort --config ./config.yml
```

### Executing Gort in a container with Docker

To execute Gort in a container, you have to mount the configuration file and the Docker socket into the container at startup. The resulting command is somewhat verbose. 

```
docker run -it \
    -v ./config.yml:/config.yml \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -p 4000:4000
    getgort/gort start -v
```

As you may have noticed, this command also opens port 4000. This allows the Gort controller to access the administration API, which is required to bootstrap your Gort instance.

To avoid having to type this command repeatedly, you can instead leverage the `docker-compose.yml` file in the `getgort/gort` repository. Once you've edited that file to contain the desired parameters, you can execute it as follows:

```
docker compose up
```

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

Congratulations, you now have a running Gort controller!

## Bootstrapping Gort

See [Bootstrapping Gort](bootstrapping.md).
