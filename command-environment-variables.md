# Command Environment Variables

Whenever Gort executes a command, it automatically injects a number of variables into the process' execution environment. 

In general, Gort-related environment variables will be prefixed with GORT_.

- `GORT_BUNDLE`: The name of the bundle the current command is a member of.
- `GORT_CHAT_HANDLE`: The chat handle of the user executing the command. This is the bare handle: a Slack user "@user" will result in a value of "user".
- `GORT_COMMAND`: The name of the current command being executed. Does not include the bundle; that is, when executing the command twitter:tweet, for example, GORT_COMMAND will equal "tweet".
- `GORT_INVOCATION_ID`: A unique ID string uniquely identifying the invocation.
- `GORT_ROOM`: The chat room where the command was invoked. This is the bare room name: if it was executed in the "#general" channel, this value would be "general". For direct messages, this will be the string "direct".
- `GORT_SERVICE_TOKEN`: A unique token for accessing Gort's service infrastructure.
- `GORT_SERVICES_ROOT`: Host and port for accessing Gort's service infrastructure.

All other environment variables (e.g., `PATH`, `USER`, etc.) are inherited from the execution container.
