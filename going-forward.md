# Going Forward: Features to Look Forward To

The following is a list of major features that are planned for version 1.0:

1. **Kubernetes support.** The ability to spawn workers using the K8S API instead of requiring a Docker port (Priority: very high)
1. **Dynamic command configuration.** Will allow setting of sensitive values like tokens or passwords for commands. (Priority: high)
1. **Triggers.** Allow Gort to execute existing bundled commands in response to non-command chat input. (Priority: medium-high)
1. **Custom webhooks.** Expose custom (auth-gated) RESTful endpoints that can be used to execute existing bundled commands. (Priority: medium)
1. **Command output formatting.** Allow command output to be formatted to make things nice and pretty. (Priority: medium)
1. **Internal key/value store.** Allow commands access to a scoped key/value store for some minor state, a little like cookies (Priotity: medium)
1. **Support for other chat platforms.** Microsoft Teams first, others TBD (Priority: medium-low)

Have any other ideas? [We want to hear them!](https://github.com/getgort/gort/issues)
