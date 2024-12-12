# Concourse Reference

The `fly` tool is used to update Concourse pipelines.

## Set up Fly

```shell
brew install fly;
fly --target=foo login --team-name=bar --concourse-url=https://example.com/;
# click the output browser link to log in
fly --target=foo sync;
```

## Inspect pipeline containers

Should a pipeline fail, Concourse preserves the containers for inspection, e.g.:

```shell
fly intercept --target=foo --job=bar/baz;
```

## Download reports

```shell
# connect to container
fly intercept --target=foo --job=bar/baz;

# within container, create archive of report
cd git/foo/build/reports/tests/;
tar -vzcf test.tar.gz test;
# note path to archive, e.g. /tmp/build/80754af9/git/foo/build/reports/tests/test.tar.gz
exit;

# cat container archive to local archive
fly hijack \
    --target=foo \
    --job=bar/baz \
    --build=7 \
    --step=build \
    cat /tmp/build/80754af9/git/foo/build/reports/tests/test.tar.gz > ./test.tar.gz;
```
