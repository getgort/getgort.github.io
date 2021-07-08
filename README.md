# The Gort Guide

***Gort is a work in progress under active heavy development. It is not production (or even alpha) ready! Follow [the Gort project](https://github.com/getgort/gort) for updates!***

Gort is a chatbot framework designed from the ground up for chatops.

Gort brings the power of the command line to the place you collaborate with your team all the time -- your chat window. Its open-ended command bundle support allows developers to implement functionality in the language of their choice, while powerful access control means you can collaborate around even the most sensitive tasks with confidence. A focus on extensibility and adaptability means that you can respond quickly to the unexpected, without your team losing visibility.

## Features

The primary goal of this project is to re-implement the core features of Cog that made it stand out among other chatops tools. Specifically, to:

* define arbitrary command functionality in any programming language,
* package those commands into bundles that can be installed in Gort,
* allow users to trigger commands through Slack or another chat provider and be presented with the output,
* execute triggered commands anywhere a relay is installed using a tag-based targeting system,
* regulate the use of commands with a built-in authentication/authorization system,
* and record activity in an audit log.

## User Documentation

1. [Quick Start](quickstart.md)
1. [Gort Architecture](architecture.md)
1. [Getting Started](getting-started.md)
1. [Controller Configuration](controller-configuration.md)
1. [Bootstrapping Gort](bootstrapping.md)
1. [Commands and Bundles](commands-and-bundles.md)
1. [Managing Bundles](managing-bundles.md)
1. [Bundle Configurations](bundle-configurations.md)
1. [Permissions and Rules](permissions-and-rules.md)
1. [Command Execution Rules](command-execution-rules.md)
1. [Command Environment Variables](command-environment-variables.md)
1. [Returning Data](returning-data.md)
1. [User Management](user-management.md)
1. [Audit Log Events](audit-log-events.md)
1. [Going Forward: Features to Look Forward To](going-forward.md)

<!-- 
COLD STORAGE:
1. [Designing ChatOps Commands](designing-chatops-commands.md)
1. [Installing Your First Command Bundle](installing-your-first-command-bundle.md)
1. [Writing a Command Bundle](writing-a-command-bundle.md)
1. [Templates](templates.md)
1. [Dynamic Command Configuration](dynamic-command-configuration.md)
1. [Configuring Password Resets](configuring-password-resets.md)
1. [Installing And Managing Relays](installing-and-managing-relays.md)
1. [Relay Configuration](relay-configuration.md)
1. [Command Output Tags](command-output-tags.md)
1. [Services](services.md)
-->