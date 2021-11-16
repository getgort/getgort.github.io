# Command Bundles

Bundles (or "command bundles") are the packaging unit for collections of one or more commands. They are sets of related commands that, when installed in Gort, may be executed by users from any connected (and allowed) chat service.

## Bundle Configurations

A bundle configuration is a [YAML](https://yaml.org/)-formatted document that describes a single bundle, including its name, description, version, [container image](commands-as-containers.md), and one or more commands. Additionally, each command definition includes a name, description, and which binary in the container to execute when the command is triggered by a user.

See [Bundle Configurations](bundle-configurations.md) for more information.

## Permissions and Rules

Importantly, each command also includes one or more _rules_, which allows operators fine-grained control over who is able to execute chat commands, extending even to control over particular invocations of chat commands.. Permissions are namespaced to the bundle they originate from, so installing a bundle's permissions will never conflict with any existing rules. Except for `admin`, permissions are never automatically assigned to users.

See [Permissions and Rules](permissions-and-rules.md) for more information.

## Writing a Command Bundle

Each references a single [Docker container image](https://www.docker.com/resources/what-container) that contains all the binaries and other dependencies for executing one or more commands. They also include some data about the commands, including a small amount of documentation and other metadata.

See [Writing A Command Bundle](writing-a-command-bundle.md) for more specifics.

## Managing Bundles

Bundles can be installed into Gort by an administrator (or any user with the `manage_commands` permission) using the `gort` command-line utility.

See [Managing Bundles](managing-bundles.md) for more on bundle installation.