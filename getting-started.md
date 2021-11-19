# Getting Started

The Gort controller is the core Gort service. It relays messages to and from the chat providers, manages relay command instructions, and exposes the REST administration API. See [Gort Architecture](architecture.md) for more information.

This guide will instruct you through the process of configuring, installing, running, and bootstrapping the Gort controller.

## 1. Configuring Gort

Gort is configured via a single [YAML](https://en.wikipedia.org/wiki/YAML)-formatted configuration file, typically called `config.yml`, which is used to describe everything from database and chat provider settings to default command bundles.

If the configuration file is changed, Gort can be instructed to "hot reload" its by sending it a SIGHUP or issuing a `GET` request to its `v2/reload/` endpoint. If the new configuration is not well-formed, the changes will not be applied.

See [Configuring Gort](configuration.md) for more detail.

## 2. Deploying Gort

Gort can be installed in a variety of ways: it can be run as a standalone binary, or as a Docker container, or in Kubernetes.

See [Deploying Gort](deployment.md) for more detail.

## 3. Bootstrapping Gort

Once Gort is deployed, the database must be set up and the initial administration user defined, a process referred to as "bootstrapping". Once Gort is properly bootstrapped, the administrator will be able to [manage users](managing-users.md), [install and enable command bundles](managing-bundles.md), and more.

See [Bootstrapping Gort](bootstrapping.md) for more detail.
