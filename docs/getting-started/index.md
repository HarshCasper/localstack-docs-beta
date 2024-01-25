---
sidebar_position: 1
---

# Installation

Basic installation guide to get started with LocalStack on your local machine.

## LocalStack CLI

The quickest way get started with LocalStack is by using the LocalStack CLI. It allows you to start LocalStack from your command line.
Please make sure that you have a working [`docker` environment](https://docs.docker.com/get-docker/) on your machine before moving on.

The CLI starts and manages the LocalStack docker container.
For alternative methods of managing the LocalStack container, see our alternative installation instructions.


import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

<Tabs>
<TabItem value="macos" label="macOS">

```bash
brew install localstack/tap/localstack-cli
```

</TabItem>
<TabItem value="linux" label="Linux">

```bash
curl -Lo localstack-cli-3.0.2-linux-amd64-onefile.tar.gz \
    https://github.com/localstack/localstack-cli/releases/download/v3.0.2/localstack-cli-3.0.2-linux-amd64-onefile.tar.gz
```

</TabItem>
<TabItem value="pip" label="PyPI">

```bash
python3 -m pip install --upgrade localstack
```

</TabItem>
</Tabs>

To verify that the LocalStack CLI was installed correctly, you can check the version in your terminal:

```bash
$ localstack --version
3.0.2
```

You are all set!
To use all of LocalStack's features we recommend to get a LocalStack account and set up your auth token.
Afterwards, check out our Quickstart guide to start your local cloud!

## Alternatives

Besides using the CLI, there are other ways of starting and managing your LocalStack instance:

- LocalStack Desktop: Get a desktop experience and work with your local LocalStack instance via the UI.
- LocalStack Docker Extension Use the LocalStack extension for Docker Desktop to work with your LocalStack instance.
- Docker-Compose: Use `docker-compose` to configure and start your LocalStack Docker container.
- Docker: Use the `docker` CLI to manually start the LocalStack Docker container.
- Helm: Use `helm` to create a LocalStack deployment in a Kubernetes cluster.