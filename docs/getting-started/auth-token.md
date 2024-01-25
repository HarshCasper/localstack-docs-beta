---
sidebar_position: 2
---

# Auth Token

The Auth Token is a personal identifier used for user authentication outside the LocalStack Web Application, particularly in conjunction with the LocalStack core cloud emulator. Its primary functions are to retrieve the user's license and enable access to advanced features, effectively replacing the older developer API keys.

The Auth Token remains unchanged unless manually rotated by the user, regardless of any license assignment changes. You can locate your Auth Token on the [Getting Started page](https://app.localstack.cloud/getting-started) or the [Auth Token page](https://app.localstack.cloud/workspace/auth-token) in the LocalStack Web Application.

## Managing your License

To use LocalStack, a license is required. You can get a license by registering on the [LocalStack Web Application](https://app.localstack.cloud/sign-up).Choose between a 14-day trial or explore additional features with our [paid offerring](https://app.localstack.cloud/pricing). During the trial period, you are welcome to use all the features of LocalStack.

After initiating your trial or acquiring a license, proceed to assign it to a user by following the steps outlined below:

- Visit the [Users & Licenses page](https://app.localstack.cloud/workspace/members).
- Select a user in the **Workspace Members** section for license assignment.
- Define user's role via the **Member Role** dropdown. Single users automatically receive the **Admin** role.
- Toggle **Advanced Permissions** to set specific permissions. Single users automatically receive full permissions.
- Click **Save** to complete the assignment. Single users assign licenses to themselves.

To view your own assigned license, visit the [My License page](https://app.localstack.cloud/workspace/my-license). You can further navigate to the [Auth Token page](https://app.localstack.cloud/workspace/auth-token) to view your Auth Token.

## Configuring your Auth Token

LocalStack requires the `LOCALSTACK_AUTH_TOKEN` environment variable to contain your Auth Token. You can configure your Auth Token in several ways, depending on your use case. The following sections describe the various methods of setting your Auth Token.

:::warning
-   It's crucial to keep your Auth Token confidential. Do not include it in source code management systems, such as Git repositories.
-   Be aware that if an Auth Token is committed to a public repository, it's at risk of exposure, and could remain in the repository's history, even if attempts are made to rewrite it.
-   In case your Auth Token is accidentally published, immediately rotate it on the [Auth Token page](https://app.localstack.cloud/workspace/auth-token).
:::

### Configuring your CI environment

For use in Continuous Integration (CI) or automated test environments, a CI key is necessary.  

To configure your CI key, you need to set the `LOCALSTACK_API_KEY` environment variable to your CI key.
You can find your CI key on the [CI Keys page](https://app.localstack.cloud/workspace/ci-keys) in the LocalStack Web Application.

### LocalStack CLI

You should set the `LOCALSTACK_AUTH_TOKEN` environment variable either before or during the startup of LocalStack using the `localstack` command-line interface (CLI).

```bash
export LOCALSTACK_AUTH_TOKEN=<YOUR_AUTH_TOKEN>
localstack start
```

You have the option to run your LocalStack container in the background by appending the `-d` flag to the `localstack start` command.

The `localstack` CLI automatically detects the Auth Token and appropriately conveys it to the LocalStack container.

### Docker

To start LocalStack via Docker, you need to provide the Auth Token using the `-e` flag, which is used for setting environment variables.

```bash
$ docker run \
  --rm -it \
  -p 4566:4566 \
  -p 4510-4559:4510-4559 \
  -e LOCALSTACK_AUTH_TOKEN=${LOCALSTACK_AUTH_TOKEN:- } \
  localstack/localstack-pro
```

For more information about starting LocalStack with Docker, take a look at our [Docker installation](https://docs.localstack.cloud/getting-started/installation/#docker) guide.

### Docker Compose

To start LocalStack using `docker compose`, you have to include the `LOCALSTACK_AUTH_TOKEN` environment variable in your `docker-compose.yml` file:

```yaml
environment:
  - LOCALSTACK_AUTH_TOKEN=${LOCALSTACK_AUTH_TOKEN- }
```

You can manually set the Auth Token, or use the `export` command to establish the Auth Token in your current shell session. This ensures the Auth Token is transmitted to your LocalStack container, enabling key activation.

## Licensing-related configuration

To ensure that LocalStack only starts when you can activate LocalStack Pro/Team/Enterprise, set `ACTIVATE_PRO=1` in your environment. This is set to `true` by default if using the `localstack/localstack-pro` container image. If set to 1, LocalStack will fail to start if the license key activation did not work. If set to 0, an attempt is made to start LocalStack without any licensed features. 

To avoid logging any licensing-related error messages, set `LOG_LICENSE_ISSUES=0` in your environment. Refer to our [configuration guide](https://docs.localstack.cloud/references/configuration/#localstack-pro) for more information.
