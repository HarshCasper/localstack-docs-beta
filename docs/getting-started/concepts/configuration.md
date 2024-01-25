---
sidebar_position: 3
---

# Configuration

LocalStack exposes various configuration options to control its behaviour.

These options can be passed to LocalStack as environment variables like so:

```bash
$ DEBUG=1 localstack start
```

You can also use [Profiles](#profiles).

Configurations marked as **Deprecated** will be removed in the next major version. You can find previously removed configuration variables under [Legacy](#legacy).

## Core

Options that affect the core LocalStack system.

| Variable | Example Values | Description |
| - | - | - |
| `DEBUG` | `0` (default) \|`1`| Flag to increase log level and print more verbose logs (useful for troubleshooting issues)|
| `IMAGE_NAME`| `localstack/localstack` (default), `localstack/localstack:0.11.0` | Specific name and tag of LocalStack Docker image to use.|
| `GATEWAY_LISTEN`| `0.0.0.0:4566` (default in Docker mode) `127.0.0.1:4566` (default in host mode) | Configures the bind addresses of LocalStack. It has the form `<ip address>:<port>(,<ip address>:<port>)*`. LocalStack Pro adds port `443`. |
| `LOCALSTACK_HOST`| `localhost.localstack.cloud:4566` (default) | This is interpolated into URLs and addresses that are returned by LocalStack. It has the form `<hostname>:<port>`. |
| `USE_SSL` | `0` (default) | Whether to return URLs using HTTP (`0`) or HTTPS (`1`). Changed with 3.0.0. In earlier versions this was toggling SSL support on or off. |

## Docker

Options to configure how LocalStack interacts with Docker.

| Variable | Example Values | Description |
| - | - | - |
| `DOCKER_FLAGS` | | Allows to pass custom flags (e.g., volume mounts) to "docker run" when running LocalStack in Docker. |
| `DOCKER_SOCK` | `/var/run/docker.sock` | Path to local Docker UNIX domain socket |
| `DOCKER_BRIDGE_IP` | `172.17.0.1` | IP of the docker bridge used to enable access between containers |
| `LEGACY_DOCKER_CLIENT` | `0`\|`1` | Whether LocalStack should use the command-line Docker client and subprocess execution to run Docker commands, rather than the Docker SDK. |
| `DOCKER_CMD` | `docker` (default), `sudo docker`| Shell command used to run Docker containers (only used in combination with `LEGACY_DOCKER_CLIENT`) |
| `FORCE_NONINTERACTIVE` | | When running with Docker, disables the `--interactive` and `--tty` flags. Useful when running headless. |

## Local AWS Services

This section covers configuration options that are specific to certain AWS services.

### Batch

| Variable | Example Values | Description |
| - | - | - |
| `BATCH_DOCKER_FLAGS` | `-e TEST_ENV=1337` | Additional flags provided to the batch container. Same restrictions as `LAMBDA_DOCKER_FLAGS`. |

### BigData (EMR, Athena, Glue)

| Variable | Example Values | Description |
| - | - | - |
| `BIGDATA_DOCKER_NETWORK` | | Network the bigdata should be connected to. The LocalStack container has to be connected to that network as well. Per default, the bigdata container will be connected to a network LocalStack is also connected to.
| `BIGDATA_DOCKER_FLAGS` | | Additional flags for the bigdata container. Same restrictions as `LAMBDA_DOCKER_FLAGS`.

### DynamoDB

| Variable | Example Values | Description |
| - | - | - |
| `DYNAMODB_ERROR_PROBABILITY` | Decimal value between `0.0`(default) and `1.0` | Randomly inject `ProvisionedThroughputExceededException` errors into DynamoDB API responses. |
| `DYNAMODB_HEAP_SIZE` | `256m` (default), `1G` | Sets the JAVA EE maximum memory size for DynamoDB; full table scans require more memory |
| `DYNAMODB_SHARE_DB` | `0`\|`1` | When activated, DynamodDB will use a single database instead of separate databases for each credential and region. |
| `DYNAMODB_IN_MEMORY` | `0` (default) \|`1` | When activated, DynamodDB will start in in-memory mode, which can have a faster throughput. If you use this options, both persistence and cloud pods will not work for DynamoDB |
| `DYNAMODB_OPTIMIZE_DB_BEFORE_STARTUP` | `0`\|`1` | Optimize the database tables in the store before starting |
| `DYNAMODB_DELAY_TRANSIENT_STATUSES` | `0`\|`1` | When activated, DynamoDB will introduce artificial delays in resource creation to simulate the actual cloud service more closely. Currently works only for CREATING and DELETING online index statuses. |
| `DYNAMODB_CORS` | `*` | Enable CORS support for specific allow-list list the domains separated by `,` use `*` for public access (default is `*`) |
| `DYNAMODB_REMOVE_EXPIRED_ITEMS` | `0`\|`1` | Enables [Time to Live (TTL)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) feature |

### ECS

| Variable | Example Values | Description |
| - | - | - |
| `ECS_REMOVE_CONTAINERS` | `0`\|`1` (default) | Remove Docker containers associated with ECS tasks after execution. Disabling this and dumping container logs might help with troubleshooting failing ECS tasks. |
| `ECS_DOCKER_FLAGS` | `--privileged`, `--dns 1.2.3.4` | Additional flags passed to Docker when creating ECS task containers. Same restrictions as `LAMBDA_DOCKER_FLAGS`. |

### EC2

| Variable | Example Values | Description |
| - | - | - |
| `EC2_DOCKER_FLAGS` | `--privileged` | Additional flags passed to Docker when launching containerized instances. Same restrictions as `LAMBDA_DOCKER_FLAGS`. |
| `EC2_DOWNLOAD_DEFAULT_IMAGES` | `0`\|`1` (default) | At startup, LocalStack Pro downloads latest Ubuntu images from Docker Hub for use as AMIs. This can be disabled for security reasons. |
| `EC2_MOUNT_BLOCK_DEVICES` | `1`\|`0` (default) | Whether to create and mount user-specified EBS block devices into EC2 container instances. |
| `EC2_EBS_MAX_VOLUME_SIZE` | `1000` (default) | Maximum size (in MBs) of user-specified EBS block devices mounted into EC2 container instances. |

## Miscellaneous

| Variable | Example Values | Description |
| - | - | - |
| `SKIP_SSL_CERT_DOWNLOAD` | | Whether to skip downloading the SSL certificate for localhost.localstack.cloud
| `CUSTOM_SSL_CERT_PATH` | `/var/lib/localstack/custom/server.test.pem` | Defines the absolute path to a custom SSL certificate for localhost.localstack.cloud
| `IGNORE_ES_DOWNLOAD_ERRORS` | | Whether to ignore errors (e.g., network/SSL) when downloading Elasticsearch plugins
| `OVERRIDE_IN_DOCKER` | | Overrides the check whether LocalStack is executed within a docker container. If set to `true`, LocalStack assumes it runs in a docker container. Should not be set unless necessary.
| `DISABLE_EVENTS` | `1` | Whether to disable publishing LocalStack events
| `OUTBOUND_HTTP_PROXY` | `http://10.10.1.3` | HTTP Proxy used for downloads of runtime dependencies and connections outside LocalStack itself
| `OUTBOUND_HTTPS_PROXY` | `https://10.10.1.3` | HTTPS Proxy used for downloads of runtime dependencies and connections outside LocalStack itself
| `REQUESTS_CA_BUNDLE` | `/var/lib/localstack/lib/ca_bundle.pem` | CA Bundle to be used to verify HTTPS requests made by LocalStack
| `DOCKER_HOST` | `unix:///var/run/docker.sock` (default) | Daemon socket to connect Docker. Used by the LocalStack dependency [Docker](https://docs.docker.com/engine/reference/commandline/cli/#environment-variables). |

## Debugging

| Variable | Example Values | Description |
| - | - | - |
| `DEVELOP` | | Starts a debugpy server before starting LocalStack services
| `DEVELOP_PORT` | | Port number for debugpy server
| `WAIT_FOR_DEBUGGER` | | Forces LocalStack to wait for a debugger to start the services

## Profiles

LocalStack supports configuration profiles which are stored in the `~/.localstack` config directory.
A configuration profile is a set of environment variables stored in an `.env` file in the LocalStack config directory.

Here is an example of what configuration profiles might look like:

```bash
$ tree ~/.localstack
/home/username/.localstack
├── default.env
├── dev.env
└── pro.env
```

Here is an example of what a specific environment profile looks like

```bash
$ cat ~/.localstack/pro-debug.env
LOCALSTACK_AUTH_TOKEN=XXXXX
DEBUG=1
DEVELOP=1
```

You can load a profile by either setting the `env` variable `CONFIG_PROFILE=<profile>` or the `--profile=<profile>` CLI flag when using the CLI.
Let's take an example to load the `dev.env` profile file if it exists:

```bash
$ python -m localstack.cli.main --profile=dev start
```

If no profile is specified, the `default.env` profile will be loaded.
While explicitly specified, the environment variables will always overwrite the profile.

To display the config environment variables, you can use the following command:

```bash
$ python -m localstack.cli.main --profile=dev config show
```
