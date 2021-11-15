# Configuring Gort

Gort is configured via a configuration file, which is used to describe everything from database and chat provider configurations to default command bundles.

Gort can reload its configuration during runtime whenever it detects that its file has been modified. If the new configuration is not well-formed, the changes will not be applied.

## The configuration file

Gort configured using a [YAML](https://en.wikipedia.org/wiki/YAML)-formatted configuration file. To specify which configuration file to load, use the `--config` flag to the `gort start` command.

The configuration YAML format is defined by the scheme described below. Brackets indicate that a parameter is optional. For non-list parameters the value is set to the specified default.

Generic placeholders are defined as follows:

- `<boolean>`: a boolean that can take the values `true` or `false`.
- `<duration>`: a duration string: a sequence of decimal numbers, each with optional fraction and a unit suffix: `1d`, `1h30m`, `5m`, `10s`. Valid units are "ms", "s", "m", "h".
- `<filename>`: a valid path in the current working directory.
- `<host>`: a valid string consisting of a hostname or IP (possibly followed by an optional port number).
- `<int>`: an integer value.
- `<path>`: a valid URL path.
- `<port>`: a valid port number ranging from 0 to 65535.
- `<secret>`: a regular string that is a secret, such as a password.
- `<string>`: a regular string.

The other placeholders are specified separately.

A valid example file can be found [here](https://github.com/getgort/gort/blob/main/config.yml).

The global configuration specifies parameters that are valid in all other configuration contexts. They also serve as defaults for other configuration sections.

```yaml
global:
  # How long before a command times out, in seconds. 0 means no timeout.
  [ command_timeout: <int> | default = 60 ]

# Gort controller behavior.
gort:
  [ - <gort> ... ]

# Configures Gort's PostgreSQL database.
database:
  [ - <database> ... ]

# Configures Gort's Docker host data.
docker:
  [ - <docker> ... ]

# Configures the Jaeger host, to which Gort sends internal trace telemetry.
jaeger:
  [ - <jaeger> ... ]

# A list of zero or more configurations that describe Slack workspaces that
# Gort can relay to and from.
slack:
  [ - <slack> ... ]
```

## &lt;gort&gt;

This section contains the settings for the behavior of the Gort controller.


```yaml
# Gort will automatically create accounts for new users when set.
# User accounts created this way will still need to be placed into groups
# by an administrator in order to be granted any permissions.
[ allow_self_registration: <boolean> | default = false ]

# The address to listen on for Gort's REST API.
[ api_address: <host> | default = ":4000" ]

# Controls the prefix of URLs generated for the core API. URLs may contain a
# scheme (either http or https), a host, an optional port (defaulting to 80
# for http and 443 for https), and an optional path.
[ api_url_base: <host> | default = "localhost" ]

# Enables development mode. Currently this only affects log output format.
[ development_mode: <boolean> | default = false ]

# If true, Gort can respond to commands prefixed with !, instead of only
# via direct mentions.
[ enable_spoken_commands: <boolean> | default = true ]

# If set along with tls_key_file, TLS will be used for API connections.
# This parameter specifies the path to a certificate file.
[ tls_cert_file: <filename> ]

# If set along with tls_cert_file, TLS will be used for API connections.
# This parameter specifies the path to a key file.
# The key must not be encrypted with a password.
[ tls_key_file: <filename> ]
```

## &lt;database&gt;

The `database` section is used to configure access to Gort's PostgreSQL database.

If this section is absent, the Gort controller will use an "in memory" data model. This is intended for trialing or development and absolutely, positively should not be used in production.

```yaml
# The host where Gort's PostgreSQL database lives.
[ host: <host> | default = "localhost" ]

# The port at which Gort may access its PostgreSQL database.
[ port: <port> | default = 5432 ]

# The user to connect to Gort's PostgreSQL database.
[ user: <string> ]

# The password for connecting to Gort's PostgreSQL database. Alternatively,
# this value can (and should) be specified via the GORT_DB_PASSWORD env var.
[ password: <secret> ]

# Set this to true to have Gort connect to its database using SSL.
[ ssl_enabled: <boolean> | default = false ]

# The maximum amount of time a connection may be idle. Expired connections
# may be closed lazily before reuse. If <= 0, connections are not closed due
# to a connection's idle time.
[ connection_max_idle_time: <duration> | default = 1m ]

# The maximum amount of time a connection may be reused. Expired connections
# may be closed lazily before reuse. If <= 0, connections are not closed due
# to a connection's age.
[ connection_max_life_time: <duration> | default = 10m ]

# Sets the maximum number of connections in the idle connection pool. If
# max_open_connections is > 0 but < max_idle_connections, then this value
# will be reduced to match max_open_connections.
# If n <= 0, no idle connections are retained.
[ max_idle_connections: <int> | default = 2 ]

# The maximum number of open connections to the database. If
# max_idle_connections is > 0 and the new this is less than
# max_idle_connections, then max_idle_connections will be reduced to match
# this value. If n <= 0, then there is no limit on the number of open
# connections.
[ max_open_connections: <int> ]

# How long to wait for execution of a database query to complete.
[ query_timeout: <duration> | default = 15s ]
```

## &lt;docker&gt;

This section is used to configure Gort's Docker host data. At the moment it only includes two values (which are likely to move into a relay configuration, when that becomes a thing).

```yaml
# Defines the location of the Docker port. Required.
host: <path>

# The name of a Docker network. If set, any worker containers will be
# attached to this network. This can be used to allow workers to communicate
# with a containerized Gort controller.
[ network: <string> ]
```

## &lt;jaeger&gt;

This section is used to configures the Jaeger host to which Gort sends internal trace telemetry.

```yaml
# The URL for the Jaeger collector that spans are sent to. If not set then
# no exporter will be created.
[ endpoint: <path> | default = http://localhost:14268/api/traces ]

# The username to be used in the authorization header sent for all requests
# to the collector. If not set no username will be passed.
[ username: <string> | default = gort ]

# The password to be used in the authorization header sent for all requests
# to the collector.
[ password: <secret> ]
```

## &lt;slack&gt;

This section is used to describe a Slack workspaces that Gort can receive commands from relay and relay responses to.

Note that `slack` allows multiple elements, which means that it's possible to configure Gort to interact with multiple Slack workspaces.

```yaml
# An arbitrary name for human labelling purposes.
name: <string>

# Bot User OAuth Access Token (https://api.slack.com/docs/token-types#bot)
# used to connect to Slack. You want the one that starts with "xoxb".
api_token: <string>

# The chat icon for the bot. If not defined, it uses the default for the
# configured bot.
[ icon_url: <path> ]

# The name of the bot, as it appears in Slack. Defaults to the name used
# when the bot was added to the account.
[ bot_name: <string> ]
```
