# The Gort Guide

Gort is a chatbot framework designed from the ground up for chatops.

Gort brings the power of the command line to the place you collaborate with your team: your chat window. Its open-ended command bundle support allows developers to implement functionality in the language of their choice, while powerful access control means you can collaborate around even the most sensitive tasks with confidence. A focus on extensibility and adaptability means that you can respond quickly to the unexpected, without your team losing visibility.

## Features

Gort's design philosophy emphasizes flexibility and security by allowing you to:

- Trigger commands through Slack (or another chat provider)
- Implement commands in any programming language of your choice
- Package commands into bundles that can be installed in Gort
- Organize users into groups, and permissions into roles
- Use a sophisticated identity and permission system to determine who can use commands
- Customizable system and command output at the application, bundle, and even command level
- Interact via Slack or Discord as first class chat providers (with more on the way!)
- Record all activity in an audit log

Gort lets you build commands in any language you want, using tooling you're already comfortable with, and can tightly control who can use them and how.

## History

Gort was initially conceived of as a Go re-implementation of Operable's [Cog Slack Bot](https://github.com/operable/cog), and while it remains heavily inspired by Cog, Gort has largely gone its own way.

During our initial design process, we found that many of Cog’s features, however innovative, went largely unused, and the codebase had become difficult to extend and maintain. Additionally, its implementation language -- Elixir -- had relatively few proficient developers. The solution, which was discussed for many months on the Cog Slack workspace, was to rewrite Cog from scratch in such as Go, removing some of less-used functionality and reducing complexity in the process.

This gave us the opportunity to consider and possibly redefine what Cog was meant to be. To choose the features that make sense, and to discard those that don't. In this way, Gort can be described more as a "spiritual successor" to Cog than a faithful re-implementation.

## User Documentation

1. [Gort Architecture](architecture.md) 
1. [Quick Start](quickstart.md)
1. [Getting Started](getting-started.md)
   1. [Configuring Gort](configuration.md)
   1. [Deploying Gort](deployment.md)
   1. [Bootstrapping Gort](bootstrapping.md) 
1. [Commands and Bundles](commands-and-bundles.md)
   1. [Commands as Containers](commands-as-containers.md)
   1. [Command Environment Variables](command-environment-variables.md) 
1. [Command Bundles](command-bundles.md)
   1. [Bundle Configurations](bundle-configurations.md)
   1. [Permissions and Rules](permissions-and-rules.md)
   1. [Writing a Command Bundle](writing-a-command-bundle.md) (COMING SOON!)
   1. [Installing Your First Command Bundle](installing-your-first-command-bundle.md)
   1. [Managing Bundles](managing-bundles.md)
1. [Command Execution Rules](command-execution-rules.md)
1. [Output Formatting Templates](templates.md)
   1. [The Response Envelope](templates-response-envelope.md)
   1. [Template Functions](templates-functions.md)
1. [Going Forward: Features to Look Forward To](going-forward.md)

<!-- 1. [Users, Groups, and Roles](users-groups-roles.md) (COMING SOON!)
   1. [User Management](managing-users.md) (COMING SOON!)
   1. [Group Management](managing-groups.md) (COMING SOON!)
   1. [Role Management](managing-roles.md) (COMING SOON!)
-->
<!-- 
COLD STORAGE:
1. [Designing Commands](designing-commands.md) (COMING SOON!)
1. The Commands Lifecycle
1. [Audit Log Events](audit-log-events.md) (COMING SOON!)
1. [Designing ChatOps Commands](designing-chatops-commands.md)
1. [Writing a Command Bundle](writing-a-command-bundle.md)
1. [Dynamic Command Configuration](dynamic-command-configuration.md)
1. [Configuring Password Resets](configuring-password-resets.md)
1. [Installing And Managing Relays](installing-and-managing-relays.md)
1. [Relay Configuration](relay-configuration.md)
1. [Command Output Tags](command-output-tags.md)
1. [Services](services.md)
-->
