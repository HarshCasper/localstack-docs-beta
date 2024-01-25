---
sidebar_position: 2
description: "Serverless Application Model (SAM)"
---

# Serverless Application Model (SAM)

The AWS Serverless Application Model (SAM) is a framework on top of CloudFormation to quickly develop Cloud Applications with a focus on serverless services such as S3, Lambda, API Gateway, Step Functions and more.

## AWS SAM CLI for LocalStack

To deploy SAM applications on [LocalStack](https://github.com/localstack/localstack) you can use [samlocal](https://github.com/localstack/aws-sam-cli-local), a wrapper for the [AWS SAM CLI](https://github.com/aws/aws-sam-cli).

### Installation

Simply use `pip` to install `samlocal` as a Python library on your machine:

```bash
$ pip install aws-sam-cli-local
```

### Usage

The `samlocal` command has the exact same usage as the underlying `sam` command. The main difference is that for commands like `samlocal deploy` the operations will be executed against the LocalStack endpoints (`http://localhost:4566` by default) instead of real AWS endpoints.

```bash
$ samlocal --help
```

Start using `samlocal` by deploying a [Hello World Application](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-getting-started-hello-world.html).
Please make sure to replace all `sam` calls with `samlocal` when following the AWS tutorial.

### Configuration

* `AWS_ENDPOINT_URL`: The endpoint URL (i.e., protocol, host, and port) to connect to LocalStack (default: `http://localhost:4566`).
