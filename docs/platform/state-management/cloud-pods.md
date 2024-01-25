---
sidebar_position: 1
description: "Cloud Pods"
---

# Cloud Pods

Cloud pods are persistent state snapshots of your LocalStack instance that can easily be stored, versioned, shared, and restored. Cloud Pods can be used for various purposes, such as:

- Sharing state snapshots amongst your team to foster collaborative debugging.
- Pre-seeding CI environments to bootstrap your testing pipelines automatically.
- Preparing reproducible application development & testing environments locally.

You can save and load the persistent state of Cloud Pods, you can use the Cloud Pods command-line interface (CLI). LocalStack provides a remote storage backend that can be used to store the state of your running application and share it with your team members. You can interact with the Cloud Pods over the storage backend via the LocalStack Web Application.

These Cloud Pods are securely stored within an AWS storage backend, where each user or organization is allocated a dedicated and isolated S3 bucket. The LocalStack Cloud Pods CLI utilizes secure S3 presigned URLs to directly interface with the S3 bucket, bypassing the need to transmit the state files through LocalStack Platform APIs.
