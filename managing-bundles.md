# Managing Bundles

This document details the commands used to manage bundles.

Currently, all command bundles are executed on the same machine as the Gort Controller. In [a future release](going-forward.md), support for "relays" will be added, which will allow commands to be executed on different machines running a Relay process.

<!-- All command bundles (with the exception of the embedded `gort` bundle) run under one or more Relay processes, which can be on the same machine as the Gort bot or on different machines.

To learn more about bundles or relays check out the corresponding docs. -->

## Prerequisites

For simplicity we will be using the `gort` command-line utility to demonstrate bundle management. Bundle management mostly involves use of the `bundle` subcommand. However, you aren't explicitly required to use `gort` to manage bundles: you can use the `!gort:bundle` command, or you can even make calls directly to the API if you like.

The remainder of this page assumes that you have a working Gort Controller and the `gort` utility.

## Installing Bundles

Bundles are installed by uploading bundle configurations to Gort, which then registers the bundle. Registration includes the creation of the permissions declared by the bundle, as well as any default rules specified in the bundle's metadata.

Importantly, after installation your bundle command will be available, but may not be usable yet. Before anyone can execute the new commands, make sure their user permissions are set properly. See [Permissions and Rules](permissions-and-rules.md) to learn more.

Bundles are installed via the `bundle install` sub-command in `gort`.

```
$ gort bundle install --help
Install a bundle from a bundle file.

When using this command, you must provide the path to the file, as follows:

  gort bundle install /path/to/my/bundle/config.yaml

Usage:
  gort bundle install [flags] config_path

Flags:
  -h, --help   Show this message and exit

Global Flags:
  -P, --profile string   The Gort profile within the config file to use
```

## The Bundle Configuration File

The only required argument for `gort bundle install` is the path to the bundle's config file.

All bundles have a `config` file, a yaml-formatted document that contains information for installing and executing commands in your bundle. To learn more about configuration files take a look at [Bundle Configurations](bundle-configurations).

We won't discuss bundle configurations in detail here, but minimally each must contain:

* `name` - The name of your bundle.
* `version` - The version of your bundle in semver format.
* `gort_bundle_version` - The version of the config file format (currently only version 1 is used).
* `commands` - A map of commands to be included in the bundle.

A minimal bundle config might look something like this:

```
---
gort_bundle_version: 4

name: my_bundle
version: 0.0.1
description: My bundle
commands:
  date:
    executable: [ /bin/date ]
    rules:
      - allow
```

The command to install the bundle would be something like `gort bundle install /path/to/my_bundle.yml`.

**Note: bundles are disabled when first installed. You must enable it before use.**

<!-- ## Templates

The templates flag points to a directory containing any templates for your bundle.

Templates are used by Gort to format command output. They are singular to a specific command/adapter combo. So for example; if we wanted to support both HipChat and Slack for our date command, we would need to supply two templates.

When added to the config file the templates section might look something like this:

---
...
templates:
  date:
    body: |
      ~each var=$results~
      `~$item.date~`
      ~end~
...
This works great for simple templates, but can get confusing when things start to get more complicated. To remedy that gort provides some helpers.

If you store your templates in a directory, you'll need to pass the --templates option; gort does not infer this by default. The directory should contain one directory per adapter and each adapter directory should contain a mustache file for each command. So for our date command we would have something like this:

$ tree templates
templates
└── date.greenbar
Given a structure like this gort will automatically append all of the templates in the directory to your bundle config before uploading. -->

## Enabling and Disabling Bundle Versions

When a new version of a bundle is installed, it's disabled by default. Only one version can be enabled at a time and a version must be explicitly enabled before Gort will use it to handle commands.

Enabling and disabling bundle versions is a straight-forward process. 

For example, if you already have version 1.0.0 of "my-bundle" installed:

```
$ go run . bundle versions my-bundle
BUNDLE      VERSION    STATUS
my-bundle   1.0.0      Enabled
```

You can install version 2.0.0 in a reasonably straight-forward manner:

```
$ gort bundle install /path/to/my-bundle/v2/config.yaml
$ gort bundle versions my-bundle
BUNDLE      VERSION    STATUS
my-bundle   1.0.0      Enabled
my-bundle   2.0.0      Disabled
```

As always, a newly-installed bundle is disabled by default. At this point, invoking any commands from the "my-bundle" bundle will still execute from version 1.0.0.

```
$ gort bundle info my-bundle
Name: gort
Versions: 1.0.0, 2.0.0
Status: Enabled
Enabled Version: 1.0.0
Commands: date
Permissions:
```

Switching to the new version is as simple as:

```
$ gort bundle enable my-bundle 2.0.0
$ gort bundle versions my-bundle
BUNDLE      VERSION    STATUS
my-bundle   1.0.0      Disabled
my-bundle   2.0.0      Enabled
```

From now on, any "my-bundle" command invocations will execute from version 2.0.0, using whatever access rules have been defined in that version.

And if you decide you don't like version 2.0.0 for any reason, you can always switch back to 1.0.0:

```
$ gort bundle enable my-bundle 1.0.0
$ gort bundle versions my-bundle
BUNDLE      VERSION    STATUS
my-bundle   1.0.0      Enabled
my-bundle   2.0.0      Disabled
```

Assuming that you have the required access, you can also enable and disable bundles using the `gort:bundle` chat command.

<!-- ### Relay Groups
Gort manages all of your command bundles and relays. Bundles are associated to relays via relay-groups. When a bundle is installed and assigned to a relay-group, Gort pushes the command config to the appropriate relay or relays. When a command is invoked, Gort uses the relay-group to select which relay is capable of running which command.

Relay groups are managed through gort with the relay-group sub-command. For more information read up on Installing and Managing Relays.

Optionally during bundle creation you can pass the --relay-group option multiple times.

Bundles are assigned to relays via relay groups using gort.

$ gort relay-group assign my_relay_group my_bundle
Note

The default refresh interval for a relay is 15 minutes (set in the relay configuration file - relay.conf). Be sure to wait for the specified amount time in order to see the bundle appear on the relays in the assigned relay group. -->

## Uninstalling Bundles and Bundle Versions

You may uninstall a specific version of a bundle or all versions of a bundle.

Uninstalling a specific version will remove rules and permissions only associated with that version. Uninstalling all bundle versions involves complete removal of all authorization rules governing its commands as well as deletion of all the bundle's permissions. You can re-install to restore the bundle permissions and rules. If you only wish to disable a bundle, see "Enabling and Disabling Bundle Versions" above.

Uninstalling a bundle requires the use of the `gort bundle uninstall` subcommand. 

```
$ gort bundle uninstall --help
Uninstall bundles.

Usage:
  gort bundle uninstall [flags] bundle_name version

Flags:
  -c, --clean         Uninstall all disabled bundle versions
  -x, --incompatible  Uninstall all incompatible versions of the bundle
  -a, --all           Uninstall all versions of the bundle
  --help              Show this message and exit.

Global Flags:
  -P, --profile string   The Gort profile within the config file to use
```

### Uninstalling a bundle version

Importantly, enabled bundles cannot be uninstalled. 

```
$ gort bundle uninstall date 0.1.0
Usage: gort bundle uninstall [OPTIONS] NAME [VERSION]

Error: Invalid value for "version": Cannot uninstall enabled version. Please disable the bundle first
```

Before any bundle can be uninstalled, it must first be disabled.

```
$ gort bundle disable my_bundle 0.1.0
$ gort bundle uninstall my_bundle 0.1.0
Uninstalled my_bundle 0.1.0
```

### Uninstalling all versions of a bundle

Since uninstalling all versions of a bundle can be very destructive, you must pass the `--all` flag to `gort`, or your request will fail.

```
$ gort bundle uninstall date
Error: Can't uninstall without specifying a version, or --incompatible, --all, --clean
```

It would seem that `gort bundle uninstall` needs either a version number, or an `--all` flag.

```
$ gort bundle uninstall date --all
Usage: gort bundle uninstall [OPTIONS] NAME [VERSION]

Error: date 0.1.0 is currently enabled. Please disable the bundle first.
```

This time the uninstallation failed because the bundle is still enabled.

```
$ gort bundle disable date
Disabled date

$ gort bundle uninstall date --all
Uninstalled date 0.0.1
Uninstalled date 0.0.1
Uninstalled date 0.1.0
```

Success at last.