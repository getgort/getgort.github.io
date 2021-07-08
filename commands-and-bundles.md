# Commands and Bundles

As a chatops bot, commands are central to Gort. Let's take a look at exactly what commands are, how they're organized, and how they're managed.

Let's start with an example. Entering the following into Slack:

```
!gort:help
```

You should receive a response that looks something like this:

```
I know about these commands:

curl:curl
gort:bundle
gort:group
gort:help
gort:role
gort:user
gort:version
```

Typing `!gort:help` executed the `help` command from the `gort` command bundle. From the response, you can see that the system currently has two bundles installed (`curl`, and `gort`), each of which contains one or more commands. The `curl` bundle contains a single command (also named `curl`), and the `gort` bundle contains several commands, one of which is the `help` command we just invoked.

Try calling `gort:help COMMAND` to find out more about a specific command:

```
!gort:help gort:user
```

This should provide a response like the following:

```
Part of the "gort" bundle.

Allows you to perform user administration.

Use "!gort:user --help" for more information about this command.
```

As indicated in the above output, many commands also support a dedicated `--help` argument (which is handled by the command executable, not by Gort). For example, typing `!gort:help --help` will return the following:

```
Provides information about a command.

If no command is specified, this will list all commands installed in Gort.

Usage:
  !gort:help [flags] [command]

Flags:
  -h, --help   Show this message and exit
```

## Commands

If you think of Gort as a "shared command line", then commands are like the executables in your terminal.

A given command may need some additional information that would not be shared on the "shared command line", but will have to be setup by an administrator, such as an OAuth key. See [Dynamic Command Configuration](dynamic-command-configuration.md) for more information on how to get this data for command execution.

## Bundles

Bundles (or "command bundles") are the packaging unit for collections of one or more commands. 

Each references a single [Docker container image](https://www.docker.com/resources/what-container) that contains all the binaries and other dependencies. They also include some metadata about the commands, including a small amount of documentation and other metadata. See [Writing A Command Bundle](writing-a-command-bundle.md) for more specifics.

Bundles can be installed into Gort by an administrator (or any user with the `manage_commands` permission) using the `gort` command-line utility. See [Managing Bundles](managing-bundles.md) for more on bundle installation.

### Bundle Permissions and Rules

Bundles also contain a set of permissions and authorization rules for their commands. When a bundle is installed, these permissions and accompanying rules are automatically created in the Gort system.

Since permissions are namespaced to the bundle they originate from, installing a bundle's permissions will never conflict with any existing authorization system configurations you may have made. No users are automatically assigned any of these permissions.

### Example: The `gort` Bundle

The gort bundle is a unique bundle in that it is effectively built into the bot. All Gort instances will have this bundle installed automatically, which is how the core permissions and authorization rules of the system come to be installed.

## Invoking Commands

To invoke a command, like `gort:help`, you actually have a few options.

First, you can use a "command prefix", which defaults to `!`.

```
!gort:help
```

You can also interact with the bot in 1-on-1 chat, in which case you may type commands directly; everything you type to the bot is considered a command.

```
gort:help
```

<!-- 
First, you can address the bot directly by name in a channel in which the bot is listening. Here, my bot is named Marvin:

```
@marvin gort:help
``` -->

### Shortcuts

Fully-qualifying all command names with their bundle name (i.e., `gort:help`) can get tedious for frequently-used commands.

Fortunately, Gort allows a shortcut: if a command name happens to be unique within a Gort installation (that is, no other bundles are installed that have a command with the same name), you may type the bare command. For example, `gort:help` can be replaced with just `help`, so long as no other bundles have a `help` command.

## Implementation Details

Every bundle has a Docker image that contains all of its commands.

By default, the command uses the image's [default entrypoint](https://docs.docker.com/engine/reference/builder/#entrypoint) to handle commands. However, if a command has an `executable` defined, then the given binary is used instead (like a [Docker `--entrypoint` parameter](https://docs.docker.com/engine/reference/run/#entrypoint-default-command-to-execute-at-runtime)). 

Any parameters you type into the command line are passed directly to the containerized binary, which can handle them just like a normal command-line execution. This allows you to implement your command using a CLI framework in any language you like.

See [Writing A Command Bundle](writing-a-command-bundle.md) for more details.