---
sidebar_position: 1
description: "This page: emulator"
---

# AWS Emulator

LocalStack provides an easy-to-use emulation framework for developing cloud applications. It spins up a testing environment on your local machine that provides the same functionality and APIs as the real AWS cloud environment. This page provides an overview of the different AWS services that are supported by LocalStack.

## Coverage Levels

LocalStack provides emulation services for different AWS APIs (e.g., Lambda, SQS, SNS, ...), but the level of support with the real system differs and is categorized using the following system:

|  Emulation Level        |     Description                                                    |
|----------|------------------------------------------------------------------------------------------------------------------------|
| ⭐⭐⭐⭐⭐ | Feature fully supported by LocalStack maintainers; feature is guaranteed to pass all or the majority of tests         | 
| ⭐⭐⭐⭐   | Feature partially supported by LocalStack maintainers         |               
| ⭐⭐⭐    | Feature supports basic functionalities (e.g., CRUD operations)          |                
| ⭐⭐      | Feature should be considered unstable          |                 
| ⭐       | Feature is experimental and regressions should be expected         | 
| **-**    | Feature is not yet implemented        | 


## Emulation Levels

* CRUD: The service accepts requests and returns proper (potentially static) responses. No additional business logic besides storing entities.
* Emulated: The service imitates the functionality, including synchronous and asynchronous business logic operating on service entities.


