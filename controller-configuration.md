# Gort Controller Configuration

Gort is configured via a configuration file, which is used to describe everything from database and chat provider configurations to default command bundles.

Gort can reload its configuration during runtime whenever it detects that its file has been modified. If the new configuration is not well-formed, the changes will not be applied.

## The configuration file

Gort configured using a [YAML](https://en.wikipedia.org/wiki/YAML)-formatted configuration file. To specify which configuration file to load, use the `--config` flag to the `gort start` command.

The configuration YAML format is defined by the scheme described below. Brackets indicate that a parameter is optional. For non-list parameters the value is set to the specified default.

Generic placeholders are defined as follows:

- `<boolean>`: a boolean that can take the values `true` or `false`.
- `<duration>`: a duration matching the regular expression `((([0-9]+)y)?(([0-9]+)w)?(([0-9]+)d)?(([0-9]+)h)?(([0-9]+)m)?(([0-9]+)s)?(([0-9]+)ms)?|0)`, e.g. 1d, 1h30m, 5m, 10s
- `<filename>`: a valid path in the current working directory
- `<host>`: a valid string consisting of a hostname or IP followed by an optional port number
- `<int>`: an integer value
- `<labelname>`: a string matching the regular expression `[a-zA-Z_][a-zA-Z0-9_]*`
- `<labelvalue>`: a string of unicode characters
- `<path>`: a valid URL path
- `<scheme>`: a string that can take the values `http` or `https`.
- `<secret>`: a regular string that is a secret, such as a password
- `<string>`: a regular string
- `<size>`: a size in bytes, e.g. `512MB`. A unit is required. Supported units: B, KB, MB, GB, TB, PB, EB.
- `<tmpl_string>`: a string which is template-expanded before usage

The other placeholders are specified separately.

A valid example file can be found [here](https://github.com/getgort/gort/blob/main/config.yml).

The global configuration specifies parameters that are valid in all other configuration contexts. They also serve as defaults for other configuration sections.

```yaml
global:
    # How long before a command times out, in seconds. 0 means no timeout.
    [ command_timeout_seconds: <int> | default = 60 ]

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

# Zero or more default command bundles, which are installed at startup and may
# not be removed by the API.
bundles:
    [ - <bundles> ... ]
```

## &lt;gort&gt;

More coming soon.

```yaml
# Gort will automatically create accounts for new users when set.
# User accounts created this way will still need to be placed into groups
# by an administrator in order to be granted any permissions.
[ allow_self_registration: <boolean> | default = false ]

# The address to listen on for Gort's REST API. Defaults to ":4000".
[ api_address: <host> | default = ":4000" ]

# Controls the prefix of URLs generated for the core API. URLs may contain a
# scheme (either http or https), a host, an optional port (defaulting to 80
# for http and 443 for https), and an optional path.
# Defaults to localhost
[ api_url_base: <host> | default = "localhost" ]

# Enables development mode. Currently this only affects log output format.
# Defaults to false
[ development_mode: <boolean> | default = false ]

# If true, allows Gort to respond to commands prefixed with ! instead of only
# via direct mentions. Defaults to true.
[ enable_spoken_commands: <boolean> | default = true ]

# If set along with tls_key_file, TLS will be used for API connections.
# This parameter specifies the path to a certificate file.
[ tls_cert_file: <filename> ]

# If set along with tls_cert_file, TLS will be used for API connections.
# This parameter specifies the path to a key file.
# The key must not be encrypted with a password.
[ tls_key_file: <filename> ]
```

(More coming soon)