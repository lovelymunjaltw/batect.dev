---
title: Config variables
---

:::note
This page reflects the options available in the [most recent version](https://github.com/batect/batect/releases/latest)
of Batect.
:::

Config variables allow you simplify your configuration file, and document and codify the different options available to someone using your tasks.

They are useful for a number of use cases:

- Reducing duplication in configuration files
- Simplifying management of developer-specific preferences (eg. a developer's preferred log output level)
- Simplifying management of sets of environment-specific settings (eg. managing sets of test environment connection settings for a CI server)

Config variables can be used anywhere [expressions](expressions.md) are supported. Reference config variables with the syntax `<name` or `<{name}`.

## Values

Values for config variables are taken from the following sources, with earlier values taking precedence:

- a value provided on the command line with [`--config-var <name>=<value>`](../cli.mdx#--config-var)
- a value specified in a config variables file, either the default `batect.local.yml` or specified with [`--config-vars-file`](../cli.mdx#--config-vars-file)
- the default value [specified in its definition](#default)

If a variable is referenced but no value is available for it, an error occurs.

## Built-in config variables

There is one built-in config variable:

### `batect.project_directory`

Contains the full path to the directory containing the root configuration file in use.

Normally, this is the directory containing `batect.yml`, or the directory containing the configuration file if it has been with the
[`--config-file` or `-f` command line option](../cli.mdx#--config-file-or--f).

This is particularly useful in configuration files imported into the project with [includes](../../concepts/includes-and-bundles.md). For example, if `/my-project/a.yml` contains:

```yaml title="/my-project/a.yml"
include:
  - includes/b.yml
```

And `/my-project/includes/b.yml` contains:

```yaml title="/my-project/includes/b.yml"
containers:
  my-container:
    image: alpine:1.2.3
    volumes:
      - local: <{batect.project_directory}/scripts
        container: /code/scripts
```

Then the directory `/my-project/scripts` will be mounted into the `my-container` container at `/code/scripts`.

## Names

Config variable names must start with a letter and contain only letters, digits, dashes (`-`), periods (`.`) and underscores (`_`). They must not start with `batect`.

## Definition

Each config variable definition is made up of:

### `description`

A human-readable description of the variable.

### `default`

The default value of the variable.

## Examples

### Config variable with no description or default value

```yaml
config_variables:
  log_level: {}
```

`{}` is the YAML syntax for an empty object.

### Config variable with description and default value

```yaml
config_variables:
  log_level:
    description: Log level to use for application
    default: debug
```
