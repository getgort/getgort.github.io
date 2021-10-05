# Quick Start

This page will tell you how to quickly create a Slack Bot User and get Gort up and running in a container in development mode.

## Create your Configuration File

1. Copy `config.yml` to `development.yml`.

## Create a Slack Bot User

1. If you haven't done so already, [create a new Slack workspace](https://slack.com/help/articles/206845317-Create-a-Slack-workspace).

1. Use this link to create a new Slack app: [https://api.slack.com/apps?new_app=1](https://api.slack.com/apps?new_app=1). Choose to create your app "From an app manifest".

1. Select your workspace and click "Next".

1. Copy the contents of the manifest file [_slackapp.yaml_](https://github.com/getgort/gort/blob/main/slackapp.yaml) into the code box below "Enter app manifest below", replacing the existing content. Click "Next".

1. Review the summary and click "Create" to create your app.

1. On the left-hand bar, under "Settings", click "Basic Information".

1. Under "App-Level Tokens", click "Generate Token and Scopes".

1. Enter a name for your token, click "Add Scope" and select "connections:write". Click "Generate".

1. Copy the app token that starts with `xapp-` and paste it into the `slack` section of your `development.yml` config file as `app_token`. Click "Done".

1. On the left-hand bar, under "Settings", click "Install App".

1. You'll get a screen that says something like "Gort is requesting permission to access the $NAME Slack workspace"; click "Allow"

1. At the top of the screen, you should see "OAuth Tokens for Your Workspace" containing a "Bot User OAuth Token" that starts with `xoxb-`. Copy that value, and paste it into the `slack` section of your `development.yml` config file as `bot_token`.

## Build the Gort Image

_This step requires that Docker be installed on your machine._

To build your own Gort Docker image, type the following from the root of the Gort repository:

```bash
make image
```

You can verify that this was successful by using the `docker images` command,

```bash
$ docker images
REPOSITORY     TAG            IMAGE ID       CREATED         SIZE
getgort/gort   0.8.0-beta.0   66fca0b90847   5 seconds ago   109MB
getgort/gort   latest         66fca0b90847   5 seconds ago   109MB
```

This should indicate the presence of two images (actually, one image tagged twice) named `getgort/gort`.

## Starting Containerized Gort

Finally, from the root of the Gort repository, you can start Gort by using `docker compose` as follows:

```bash
docker compose up
```

If everything works as intended, you will now be running three containers: 

1. Gort
2. Postgres (a database, to store user and bundle data)
3. Jaeger (for storing trace telemetry)

## Bootstrapping Gort

Before you can use Gort, you have to bootstrap it by creating the `admin` user.

You can do this using the `gort bootstrap` command and passing it the email address that your Slack provider knows you by, and the URL of the Gort controller API (by default this will be `localhost:4000`):

```bash
$ gort bootstrap --allow-insecure localhost:4000
User "admin" created and credentials appended to gort config.
```

## Using Gort

You should now be able to use Gort in any Slack channel that includes your Gort bot. Any Gort commands should be prepended by a `!`. For example, try typing the following in Slack:

`!echo Hello, Gort!`

If everything works as expected, you should see an output something like the following:

![Hello, Gort!](images/hello-gort.png "Hello, Gort!")

This instructs Gort to execute the `echo` command, which is part of the `echo` bundle. Alternatively, you could have specified the bundle as well by typing something like:

`!echo:echo Hello, again, Gort!`

