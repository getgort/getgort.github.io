# Installing Your First Command Bundle

Once you have a command bundle, you'll need to install and enable it for it to be useful.

## Creating Your Bundle

To create a bundle, you first need a bundle configuration: a YAML-formatted file that supplies Gort with all of the information it needs to install and execute commands in your bundle.

For a detailed description of bundle configurations, go to the [Bundle Configurations](bundle-configurations.md) section in the documentation.

For our example we will be using the following config. It's a simple bundle with only one un-enforcing command. Just create a file named `my_bundle.yaml` and paste the contents below into it. It doesn't actually matter what you name the file, just make sure that it is properly-formatted YAML and that it has the correct extension, `.yaml` or `.yml`.

**my_bundle.yaml**

```yml
---
gort_bundle_version: 1

name: example
version: 0.0.1
permissions:
  - date
docker:
  image: ubuntu
  tag: 20.04
commands:
  date:
    executable: [ "/bin/date" ]
    rules:
      - allow
```

If your commands specify any rules, other than the special "allow" rule, you will need to make sure the proper grants are in place. Check out [Permissions and Rules](permissions-and-rules.md) to learn more.

Bundles are most easily created with Gort's command line interface: `gort`. To create your bundle just type the following at the command prompt. Adjust the `my_bundle.yaml` bit to point to the config file that you created.

```
$ gort bundle install my_bundle.yaml
```

And there you have it! Bundle created. Now let's see about enabling it.

## Enabling Your Bundle

By default any bundles added to Gort are added in the disabled state. This way you don't have to worry about accidentally exposing commands that aren't fully configured, or otherwise not ready for production.

Enabling commands is easy though. We'll use the `gort` CLI! By default the highest installed version of a bundle will be enabled. To enable a different version just pass the version to enable to `gort`.

```
$ gort bundle enable my_bundle

$ gort bundle enable my_bundle 0.2.0
```

## Success!

That's it! You've successfully installed your first bundle. If everything went properly you should see the new command in Gort's command list.

```
User:
!help

Gort:
I know about these commands:

- example:date
- gort:bundle
- gort:group
- gort:help
- gort:role
- gort:user
- gort:version


Try calling `gort:help COMMAND` to find out more.
```

Finally, you should be able to run it!

```
User:
!date

Gort:
Wed Nov 17 00:10:24 UTC 2021
```
