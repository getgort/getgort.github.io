# Bootstrapping Gort

Since interactions with Gort require a user account, you'll need to bootstrap your system before you can do anything interesting with it. This process will create the necessary administrator role and user group, as well as create the first user account and place it into that administrator group. At this point, you can interact with Gort as this first privileged user in order to create other user accounts (to which you can also grant administrative access, if you like), install bundles, and perform other tasks.

The canonical way to do this is to use the `gort bootstrap` command:

```
$ gort bootstrap --allow-insecure localhost:4000
User "admin" created and credentials appended to gort config.
```

Note the existence of the `--allow-insecure` flag. This allows you to communicate with the Gort relay across an unencrypted channel, which is commonly the case when you're testing locally. _**This should never, ever be used in production.**_

The `gort bootstrap` command is idempotent: subsequent calls will return an error message, but the Gort system itself will remain unaffected.

If you take a look in `~/.gort/profile`, you'll begin to see what has happened.

```
$ cat ~/.gort/profile 

defaults:
    profile: localhost
localhost:
    url: http://localhost:4000
    password: cZO5E4i8+T6vVRO8m4EvYEyGI2fn86iZ
    user: admin
    allow_insecure: true
    tls_cert_file: ""
```

Here, we can see that a user named `admin` has been created for us on the Gort controller. A password has also been generated for this user. Now, whenever we run any `gort` commands from this machine, these credentials will be used by default to make authenticated API calls.

Gort's REST API is guarded by Gort's authorization system, which means that the `admin` user must have permissions to access the API somehow. As detailed in Permissions and Rules, permissions must be attached to a user somehow through a combination of roles and groups. As you can probably guess, the bootstrapping process handles all this. Let's use `gort` to examine what has been done.

First, let's just check which users exist.

```
$ gort user list
USERNAME  FULL NAME           EMAIL ADDRESS
admin     Gort Administrator  admin@gort
```

As you can see, there's just one: `admin`. 

Now let's examine the core permissions of the Gort system. These govern fine-grained access to the various REST API endpoints and chat commands.

```
$ gort permission list
NAME                   
gort:manage_commands   
gort:manage_groups     
gort:manage_permissions
gort:manage_roles      
gort:manage_users      
```

That's a lot of permissions. Gort helps us out by creating an `admin` role to collect them all together.

```
$ gort role info admin
Name         admin
ID           15c77231-e53f-4ab9-b438-64b4a2f636d6
Permissions  manage_commands, manage_groups, manage_permissions, manage_roles, manage_users
Groups       admin
```

Finally, we have a group that is also named `admin` with the `admin` user as its sole member. This group is granted the `admin` role.

```
$ gort group info admin
ID     88f30dec-ca13-4d92-a6bd-4631acc7424b
Name   admin
Users  admin
Roles  admin
```

Though the Gort `admin` user is named "admin", there's nothing particularly special about that name. As this tour of the bootstrapping process has shown us, the `admin` user functions as an administrator, able to perform any task in the Gort system only because it resides in a group that has been granted all the core permissions. Any user in this group would have the same capabilities.

This also shows how to make any Gort user an administrator by adding them to the `admin` group.

