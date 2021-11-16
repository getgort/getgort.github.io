# Commands As Containers

As you may recall from [Commands and Bundles](commands-and-bundles.md), the _command bundle_ is the packaging unit for a collection of one or more actions triggerable by a user, collectively referred to as "commands". Each bundle references a single [Docker container image](https://www.docker.com/resources/what-container) that contains all the binaries and other dependencies for executing one or more commands.

Each command triggered by a user is executed in a [container](https://www.docker.com/resources/what-container), executed from the image specified in the bundle definition. 

This is actually quite a powerful approach, because it allows you to construct commands using whatever language is best suited for the job at hand.

## Creating the Container

Each triggered command results in the spawning of a new _worker_, whose job it is to create, manage, and clean up the command container. Each worker is isolated from other workers, even of the same type, and does not share any data with other workers.

The type of worker created depends on the execution environment. In a plain Docker setup, the worker interacts with the Docker daemon to create and execute a container from the image specified in the command's bundle. In Kubernetes, the worker instead creates a [Kubernetes Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/) resource to manage the container.

In both cases, the new command container gets several default [Environment Variables](command-environment-variables.md) that can be useful for command processes.

## Executing the Command

However it's created, the new container executes the `executable` specified in the bundle command. If an `executable` isn't specified in the command, the container's [default `ENTRYPOINT`](https://docs.docker.com/engine/reference/builder/#entrypoint) is used instead.

By allowing a different `executable` value to be set for each command in a bundle, it becomes possible for a single container to contain many commands. See [Writing a Command Bundle](writing-a-command-bundle.md) for more information.

## Command Parameters

When a command is triggered, the entire string following the command is tokenized by splitting on whitespace (quoted words are kept together), and the resulting string array is passed to the container as if the executable was executed on the command line.

For example, the command executed in response to the trigger `!echo I want "to go" home` will receive the string `{ "I", "want", "to go", "home" }`.

The consequence of this design is that Gort has no required libraries or special structures that you need to adhere to. Because command executables receive parameters as a string array, just like any command executed on the command line, you can implement your commands using whatever CLI tooling is most appropriate for your language (Cobra in Go, Argparse in Python, OptionParser in Ruby, etc.).

## Termination

When a command process completes (or is forcefully terminated, such as when it times out), Gort captures its exit code, and cleans up (removes) the container or Kubernetes resources.

The value of this exit code [is used to determine](https://www.baeldung.com/linux/status-codes) whether the command was successful: an exit status of 0 indicates success; any other value is assumed to indicate an error.

## Output

When the container runs, Gort retrieves its `stdout` and `stderr` as a single stream to form the command output. If the command terminates with an error, this is assumed to be an error message.

## Presentation

Finally, the output is presented to the user. If the output consists of valid JSON, it will be sent to the templating engine and transformed according to the appropriate [Output Formatting Templates](templates.md) (you don't have to worry about that now -- there are perfectly reasonable defaults). Otherwise the output will be formatted for the user as simple, monospaced text.

## Additional Reading

Discussions of constructing commands and formatting its output at [Writing a Command Bundle](writing-a-command-bundle.md)