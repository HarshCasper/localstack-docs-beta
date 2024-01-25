---
sidebar_position: 2
description: "Terraform"
---

# Terraform

[Terraform](https://terraform.io/) is an Infrastructure-as-Code (IaC) framework developed by HashiCorp. It enables users to define and provision infrastructure using a high-level configuration language. Terraform uses HashiCorp Configuration Language (HCL) as its configuration syntax. HCL is a domain-specific language designed for writing configurations that define infrastructure elements and their relationships.

LocalStack supports Terraform via the [AWS provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs) through [custom service endpoints](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/guides/custom-service-endpoints#localstack). You can configure Terraform to use LocalStack in two ways:

-   Using the [`tflocal` wrapper script](https://github.com/localstack/terraform-local) to automatically configure the service endpoints for you.
-   Manually configuring the service endpoints in your Terraform configuration with additional maintenance.

In this guide, we will demonstrate how you can create local AWS resources using Terraform and LocalStack, by using the `tflocal` wrapper script and a manual configuration example.

## `tflocal` wrapper script

`tflocal` is a small wrapper script to run Terraform against LocalStack. `tflocal` script uses the [Terraform Override mechanism](https://www.terraform.io/language/files/override) and creates a temporary file `localstack_providers_override.tf` to configure the endpoints for the AWS `provider` section. The endpoints for all services are configured to point to the LocalStack API (`http://localhost:4566` by default). It allows you to easily deploy your unmodified Terraform scripts against LocalStack.

### Create a Terraform configuration

Create a new file named `main.tf` and add a minimal S3 bucket configuration to it. The following contents should be added in the `main.tf` file:

```hcl
resource "aws_s3_bucket" "test-bucket" {
  bucket = "my-bucket"
}
```

### Install the `tflocal` wrapper script

To install the `tflocal` command, you can use `pip` (assuming you have a local Python installation):

```bash
$ pip install terraform-local
```

After installation, you can use the `tflocal` command, which has the same interface as the `terraform` command line.

```bash
$ tflocal --help
Usage: terraform [global options] <subcommand> [args]
...
```bash

### Deploy the Terraform configuration

Start your LocalStack container using your preferred method. Initialize Terraform using the following command:

```bash
$ tflocal init
```

You can now provision the S3 bucket specified in the configuration:

```bash
$ tflocal apply
```

### Configuration

| Environment Variable     | Default value                    | Description |
| ------------------------ | -------------------------------- | ----------- |
| `TF_CMD`                 | `terraform`                        | Terraform command to call |
| `AWS_ENDPOINT_URL`       | -                                | Hostname and port of the target LocalStack instance |
| `LOCALSTACK_HOSTNAME`    | `localhost`                        | **(Deprecated)** Host name of the target LocalStack instance |
| `EDGE_PORT`              | `4566`                             | **(Deprecated)** Port number of the target LocalStack instance |
| `S3_HOSTNAME`            | `s3.localhost.localstack.cloud`    | Special hostname to be used to connect to LocalStack S3 |
| `USE_EXEC`               | -                                | Whether to use `os.exec` instead of `subprocess.Popen` (try using this in case of I/O issues) |
| `<SERVICE>_ENDPOINT`     | -                                | Setting a custom service endpoint, e.g., `COGNITO_IDP_ENDPOINT=http://example.com` |
| `AWS_DEFAULT_REGION`     | `us-east-1`                        | The AWS region to use (determined from local credentials if `boto3` is installed) |
| `CUSTOMIZE_ACCESS_KEY`   | -                                | Enables you to override the static AWS Access Key ID |
| `AWS_ACCESS_KEY_ID`      | `test` (`accountId`: 000000000000)   | AWS Access Key ID to use for multi-account setups |

:::note

While using `CUSTOMIZE_ACCESS_KEY`, following cases are taking precedence over each other from top to bottom:
1.  If the `AWS_ACCESS_KEY_ID` environment variable is set.
2.  If `access_key` is configured in the Terraform AWS provider.
3.  If the `AWS_PROFILE` environment variable is set and properly configured.
4.  If the `AWS_DEFAULT_PROFILE` environment variable is set and configured.
5.  If credentials for the `default` profile are configured.
6.  If none of the above settings are present, it falls back to using the default `AWS_ACCESS_KEY_ID` mock value.

:::