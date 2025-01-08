# Remote Redis Connection Setup

Follow these steps to interact with an Azure Redis from a local machine.

## Install command line tools

```shell
brew install redis; # (1)!
brew install stunnel; # (2)!
```

1. The Redis command line interface
2. A tool for SSL tunneling

## Fetch access key

Go to the [Azure portal](https://portal.azure.com/) and look up the target Redis resource.
The resource page will have an "Access keys" link under the "Settings" section of the left navigation.
Note the following information, which will be used to configure the local machine:

1. Primary access key
2. Host and port, e.g. my-resource.redis.cache.windows.net:6380

Save the access key to the `REDISCLI_AUTH` environment variable, e.g. add the following line to `~/.zshrc`:

```shell
export REDISCLI_AUTH="<access token>"
```

## Configure Stunnel

If it doesn't exist already, create a Stunnel configuration file at `/usr/local/etc/stunnel/stunnel.conf`.
Add the following entry to it:

```text
[redis-cli]
client = yes
accept = 127.0.0.1:6380
connect = <host and port copied from Azure>
```

## Connect to Redis

```shell
stunnel;
redis-cli -p 6380; # (1)!
```

1. Uses the value of `REDISCLI_AUTH` for the password

## Execute Redis commands

```redis
ping
keys *
smembers my-set
```
