# Going Forward: Features to Look Forward To

The following is a list of major features that are planned for version 1.0:

1. **Kubernetes support.** The ability to [spawn workers using the K8S API](https://github.com/getgort/gort/issues/95) instead of requiring a Docker port. (Priority: very high)
1. **Dynamic command configuration.** Allow dynamic values like tokens and passwords to be [securely passed to commands at runtime](https://github.com/getgort/gort/issues/114). (Priority: high)
1. **Triggers.** Allow Gort to execute existing bundled commands [in response to non-command chat input](https://github.com/getgort/gort/issues/117). (Priority: medium-high)
1. **Custom webhooks.** Expose [custom (auth-gated) RESTful endpoints](https://github.com/getgort/gort/issues/118) that can be used to execute existing bundled commands. (Priority: medium)
1. **Command output formatting.** Allow [custom formatting of command output](https://github.com/getgort/gort/issues/114), to make things nice and pretty. (Priority: medium)
1. **Internal key/value store.** Allow commands access to [an internally-scoped key/value store](https://github.com/getgort/gort/issues/128) to maintain state, a little like HTTP cookies. (Priotity: medium)
1. **Support for other chat platforms.** [Microsoft Teams](https://github.com/getgort/gort/issues/121) first, others TBD (Priority: medium-low)

Have any other ideas? [We want to hear them!](https://github.com/getgort/gort/issues)
