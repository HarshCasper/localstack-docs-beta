---
sidebar_position: 1
description: "Testing Utils"
---

# Testing Utils

LocalStack provides a set of tools to simplify application testing on LocalStack. These tools are available for Python and JVM (Java and Kotlin) and can be used to integrate with various unit testing frameworks and simplify the setup of AWS clients with LocalStack.

## Python

This Python Testing Utils streamlines the integration of Localstack with your unit tests.

### Installation

```bash
$ pip install localstack-utils
```

### Usage

```python3
import time
import boto3
import unittest
from localstack_utils.localstack import startup_localstack, stop_localstack

class TestKinesis(unittest.TestCase):
    def setUp(self):
        startup_localstack()

    def tearDown(self):
        stop_localstack()
        return super().tearDown()

    def test_create_stream(self):
        kinesis = boto3.client(
            service_name="kinesis",
            aws_access_key_id="test",
            aws_secret_access_key="test",
            endpoint_url="http://localhost:4566",
        )

        kinesis.create_stream(StreamName="test", ShardCount=1)
        time.sleep(1)

        response = kinesis.list_streams()
        self.assertGreater(len(response.get("StreamNames", [])), 0)
```

## JVM

LocalStack provides [Java Utils library](https://github.com/localstack/localstack-java-utils)
that integrates with JUnit and provides LocalStack-targeted AWS Clients.

### Installation

```maven
<dependency>
    <groupId>cloud.localstack</groupId>
    <artifactId>localstack-utils</artifactId>
    <version>0.2.15</version>
    <scope>test</scope>
</dependency>
```

### Usage

```java
...
import cloud.localstack.LocalstackTestRunner;
import cloud.localstack.ServiceName;
import cloud.localstack.TestUtils;
import cloud.localstack.docker.annotation.LocalstackDockerProperties;

@RunWith(LocalstackTestRunner.class)
@LocalstackDockerProperties(services = { ServiceName.S3, "sqs", "kinesis" })
public class MyCloudAppTest {

  @Test
  public void testLocalS3API() {
    AmazonS3 s3 = TestUtils.getClientS3()
    List<Bucket> buckets = s3.listBuckets();
    ...
  }

}
```

#### Powermock

You can use the PowerMock Library to call the builders default method and still get LocalStack version of the AWS clients.

```java
...
@RunWith(PowerMockRunner.class)
@PowerMockRunnerDelegate(LocalstackTestRunner.class)
@LocalstackDockerProperties(services = { "ses" })
@PrepareForTest({ AmazonSimpleEmailServiceClientBuilder.class, AmazonSimpleEmailServiceAsyncClientBuilder.class })
@PowerMockIgnore({"javax.crypto.*", "org.hamcrest.*", "javax.net.ssl.*", "com.sun.org.apache.xerces.*", "javax.xml.*", "org.xml.*", "javax.management.*", "javax.security.*", "org.w3c.*"})
public class SESMessagingTest {
....
    @Before
    public void mockSES() {
        AmazonSimpleEmailService mockSes = TestUtils.getClientSES();
        PowerMockito.mockStatic(AmazonSimpleEmailServiceClientBuilder.class);
        when(AmazonSimpleEmailServiceClientBuilder.defaultClient()).thenReturn(mockSes);
    }
    @Test
    public void testSendEmail() throws Exception {
        AmazonSimpleEmailService client = amazonSimpleEmailServiceClientBuilder.defaultClient();
    ....
```

#### PowerMockLocalStack Utility

This utility makes easier to use PowerMock with LocalStack.

```java
...
public class PowerMockLocalStackExampleTest extends PowerMockLocalStack{
    private static final String TOPIC = "topic";
    @Before
    public void mock() {
        PowerMockLocalStack.mockSNS();
    }

    @Test
    public void testSendMessage() throws JMSException {
        final AmazonSNS clientSNS = AmazonSNSClientBuilder.defaultClient();
        ...
    }
}
```
