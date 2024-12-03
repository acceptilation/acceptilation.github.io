# Safe Setup

Follow these steps to interact with Vault via the [Safe][1] command line interface.

## Install Safe

```shell
brew install starkandwayne/cf/safe;
safe --version;
```

## Target Vault

```shell
safe target [url] [alias];
```

## Log in

```shell
safe auth ldap;
```

## Execute commands

```shell
safe tree some/path;
safe get some/path/to/secret;
```

[1]: https://github.com/starkandwayne/safe
