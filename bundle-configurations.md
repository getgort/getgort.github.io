# Bundle Configurations

A command bundle is a set of related commands that, when installed in Gort, may be executed by users from any connected (and allowed) chat service. A bundle configuration specifies which binary to execute, and who may execute the commands (i.e., which users with which permissions).

*Currently, Gort only supports commands that have been built into a Docker image, but a future iteration will support the execution of commands natively on a relay's host.*

## A Minimal Bundle Configurations

A minimal bundle configuration looks like the following:

```yaml
---
gort_bundle_version: 4

name: my_bundle
description: "Does bundle things"
version: 0.1

docker:
  image: ubuntu
  tag: 20.04

commands:
  date:
    executable: [ "/usr/local/bin/mydate" ]
    description: "Displays the current date and time"
    rules:
      - allow
```

The `name`, `description`, `version`, `gort_bundle_version` and `commands` fields are all required. Let's go over what these do:

* `gort_bundle_version` is the version of Gort's bundle system that your bundle was built for. The current bundle version is 1, which is used through out the rest of this document.

* `name` is the name of your bundle, which also serves as the namespace under all of the bundle's commands are installed. In this case the date command's fully-qualified name is `my_bundle:date`.

* `description` is a short, one-line description for your bundle. This will be printed along with a list of all installed bundles when a user runs the `!gort:help` command.

* `version` is the [semver](https://semver.org) version number of your bundle. If you want to install a new version of bundle then you first need to uninstall the old one.

* `docker` specifies the Docker image that contains all of the bundle's commands. Limit: one image per bundle.

* `commands` are a map of zero or more commands that can be invoked in the bundle, and their associated executables. The command name, as defined here, will be the command invoked by users; it doesn't have to match the name of the binary.

### Commands

Commands are possibly the most complex component of the bundle config.

As an example let’s look at an excerpt from the config for a `ec2` bundle.

```yaml
...
commands:
  find:
    executable: [ /usr/local/bin/ec2_find ]
    description: Finds an AWS EC2 instance
    rules:
      - must have ec2:view
...
```

Here you can see the command name, "find", under which are nested several fields.

* `executable` points to the command script or binary that's to be run when the command is executed. Optional. If omitted, this defaults to the image's specified [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint).

* `description` is a short, one-line description for the command. This is the info that will appear along with a list of commands when a user runs the `help` command.

* `rules` is a required list of strings that define what permissions are required to run the command. In this example, the `ec2:view` permission is required. See [Permissions and Rules](permissions-and-rules.md) to learn more about rules and their construction.

### Permissions

Most commands require permissions to run. Permissions are specified by in the bundle config as a list of strings at the top level. Here is another excerpt of the `ec2` config as an example.

```yaml
---
gort_bundle_version: 1

name: ec2
description: Manage EC2 instances and related services
version: 0.4.0
permissions:
- view
- destroy
- create

...
```

In this example, three permissions are defined. When being referenced in a command rule a permission's fully-qualified name must be used: e.g., `ec2:view` or `ec2:destroy`.

### Documentation fields

There are a number of fields dedicated to rendering help output via the `help` command, both for the bundle and the command.

#### Bundle

The following documentation fields can also be used at the top level of a bundle configuration:

* `long_description` is a separate section for a longer form description, which can include things like what configuration is required, how commands should be used, and more details about the underlying implementation.
* `author` is where the bundle author can leave their name and email address if a user needs their contact information.
* `homepage` is a URL for the bundle, typically a GitHub repository.

#### Command

The following documentation field can also be used in each command configuration:

* `long_description` is a long-form description used to explain details of a command that don’t fit into other sections like an explanation of required arguments or about the structure of the output.

<!-- * `examples` is how a user will run the command and what output they should expect.

* `notes` is a free-form section at the bottom of the command above author and homepage -->

## Bundle Installation

Command bundles can installed in one of two ways: 

1. Explicitly installed by an administrator using the `gort bundle install` subcommand, or
1. "By default" by describing them in the Gort controller configuration, or

The YAML used for each of these cases is nearly identical.

### Explicitly-installed bundles

Command bundles can be explicitly installed using `gort bundle install`. Bundles can only be installed this way by an adequately-privileged user (generally an administrator), and are disabled by default.

See [Managing Bundles](managing-bundles.md) for more information on how to explicitly install command bundles.

### Default bundles

Default bundles are described in the `bundles` section of the [Controller Configuration](controller-configuration.md), and are automatically installed and enabled when the controller starts up. 

An example excerpt `bundles` section of a controller configuration is shown below:

```yaml
...

bundles:
- name: echo
  author: Matt Titmus <matthew.titmus@gmail.com>
  homepage: https://getgort.github.io/gort-guide/
  description: A test bundle.
  long_description: |-
    This is test bundle.
    There are many like it, but this one is mine.

  docker:
    image: clockworksoul/relaytest
    tag: latest

  permissions:
    - echo

  commands:
    echo:
      description: "Echos back anything sent to it, all at once."
      executable: "/bin/echo"
      rules:
        - must have test:echo
```

As you can see, the bundle YAML format is virtually identical to that used for explicitly-installed bundles, except:

- It's embedded in a `bundles` block
- It doesn't have a `gort_bundle_version` attribute
- It doesn't have a `version` attribute.

Bundles installed this way are automatically enabled, and can't be removed or disabled.

## A Complete Bundle Configuration Example

Below is a complete example of a bundle configuration. In fact, it's the default bundle used by Gort to install the `gort` bundle (minus a few commands, cut for brevity).

```yaml
---
gort_bundle_version: 1

name: gort
version: 0.0.1
author: Matt Titmus <matthew.titmus@gmail.com>
homepage: https://getgort.github.io/gort-guide/
description: The default command bundle.
long_description: |-
  The default command bundle, which contains the administrative commands and
  the permissions required to use them.
  Don't change or override this unless you know what you're doing.

permissions:
  - manage_commands
  - manage_groups
  - manage_roles
  - manage_users

docker:
  image: getgort/gort
  tag: v0.9.0

commands:
  bundle:
    description: "Perform operations on bundles"
    long_description: |-
      Allows you to perform bundle administration.

      Usage:
        gort:bundle [command]

      Available Commands:
        disable     Disable a bundle by name
        enable      Enable the specified version of the bundle
        info        Info a bundle
        install     Install a bundle
        list        List all bundles installed
        uninstall   Uninstall bundles
        yaml        Retrieve the raw YAML for a bundle.

      Flags:
        -h, --help   help for bundle
    executable: [ "/bin/gort", "bundle" ]
    rules:
      - must have gort:manage_commands

  version:
    description: "Displays version and build information"
    long_description: |-
      Displays version and build information.

      Usage:
        gort:version [flags]

      Flags:
        -h, --help    help for version
        -s, --short   Print only the version number
    executable: [ "/bin/gort", "version" ]
    rules:
      - allow

  help:
    description: "Provides information about a command"
    long_description: |-
      Provides information about a command.

      If no command is specified, this will list all commands installed in Gort.

      Usage:
        gort:help [flags] [command]
    executable: [ "/bin/gort", "hidden", "commands" ]
    rules:
      - allow
```