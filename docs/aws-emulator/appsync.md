---
sidebar_position: 3
description: "AppSync"
tags: ["appsync", "graphql"]
---

# AppSync

AppSync is a managed service provided by Amazon Web Services (AWS) that enables you to create serverless GraphQL APIs to query databases, microservices, and other APIs. AppSync allows you to define your data models and business logic using a declarative approach, and connect to various data sources, including other AWS services, relational databases, and custom data sources.

LocalStack supports AppSync via the Pro/Team offering, allowing you to use the AppSync APIs in your local environment to connect your applications and services to data and events. The supported APIs are available on our [API coverage page](https://docs.localstack.cloud/references/coverage/coverage_appsync/), which provides information on the extent of AppSync's integration with LocalStack.

## Getting started

This guide is designed for users new to AppSync and assumes basic knowledge of the AWS CLI and our [`awslocal`](https://github.com/localstack/awscli-local) wrapper script.

Start your LocalStack container using your preferred method. We will demonstrate how to create an AppSync API with a DynamoDB data source using the AWS CLI.

### Create a DynamoDB table

You can create a DynamoDB table using the [`CreateTable`](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_CreateTable.html) API. Execute the following command to create a table named `DynamoDBNotesTable` with a primary key named `NoteId`:

```bash
$ awslocal dynamodb create-table \
    --table-name DynamoDBNotesTable \
    --attribute-definitions AttributeName=NoteId,AttributeType=S \
    --key-schema AttributeName=NoteId,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST
```

After the table is created, you can use the [`ListTables`](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_ListTables.html) API. Run the following command to list all tables in your running LocalStack container:

```bash
$ awslocal dynamodb list-tables
```

The following output would be retrieved:

```bash
{
    "TableNames": [
        "DynamoDBNotesTable"
    ]
}
```

### Create a GraphQL API

You can create a GraphQL API using the [`CreateGraphqlApi`](https://docs.aws.amazon.com/appsync/latest/APIReference/API_CreateGraphqlApi.html) API. Execute the following command to create a GraphQL API named `NotesApi`:

```bash
$ awslocal appsync create-graphql-api \
    --name NotesApi \
    --authentication-type API_KEY
```

The following output would be retrieved:

```bash
{
    "graphqlApi": {
        "name": "NotesApi",
        "apiId": "014d18d0c2b149ee8b66f39173",
        "authenticationType": "API_KEY",
        "arn": "arn:aws:appsync:us-east-1:000000000000:apis/014d18d0c2b149ee8b66f39173",
        "uris": {
            "GRAPHQL": "http://localhost:4566/graphql/014d18d0c2b149ee8b66f39173",
            "REALTIME": "ws://localhost:4510/graphql/014d18d0c2b149ee8b66f39173"
        },
        "tags": {},
        "xrayEnabled": false
    }
}
```

You can now create an API key for your GraphQL API using the [`CreateApiKey`](https://docs.aws.amazon.com/appsync/latest/APIReference/API_CreateApiKey.html) API. Execute the following command to create an API key for your GraphQL API:

```bash
$ awslocal appsync create-api-key \
    --api-id 014d18d0c2b149ee8b66f39173
```

The following output would be retrieved:

```bash
{
    "apiKey": {
        "id": "31d94a05",
        "expires": 1693551600
    }
}
```

### Create a GraphQL schema

Create a file named `schema.graphql` with the following content:

```graphql
type Note {
  NoteId: ID!
  title: String
  content: String
}
type PaginatedNotes {
  notes: [Note!]!
  nextToken: String
}
type Query {
  allNotes(limit: Int, nextToken: String): PaginatedNotes!
  getNote(NoteId: ID!): Note
}
type Mutation {
  saveNote(NoteId: ID!, title: String!, content: String!): Note
  deleteNote(NoteId: ID!): Note
}
type Schema {
  query: Query
  mutation: Mutation
}
```

You can start the schema creation process using the [`StartSchemaCreation`](https://docs.aws.amazon.com/appsync/latest/APIReference/API_StartSchemaCreation.html) API. Execute the following command to start the schema creation process:

```bash
$ awslocal appsync start-schema-creation \
    --api-id 014d18d0c2b149ee8b66f39173 \
    --definition file://schema.graphql
```

The following output would be retrieved:

```bash
{
    "status": "ACTIVE"
}
```

### Create a data source

You can create a data source using the [`CreateDataSource`](https://docs.aws.amazon.com/appsync/latest/APIReference/API_CreateDataSource.html) API. Execute the following command to create a data source named `DynamoDBNotesTable`:

```bash
$ awslocal appsync create-data-source \
    --name AppSyncDB \
    --api-id 014d18d0c2b149ee8b66f39173 \
    --type AMAZON_DYNAMODB \
    --dynamodb-config tableName=DynamoDBNotesTable,awsRegion=us-east-1
```

The following output would be retrieved:

```bash
{
    "dataSource": {
        "dataSourceArn": "arn:aws:appsync:us-east-1:000000000000:apis/014d18d0c2b149ee8b66f39173/datasources/AppSyncDB",
        "name": "AppSyncDB",
        "type": "AMAZON_DYNAMODB",
        "dynamodbConfig": {
            "tableName": "DynamoDBNotesTable",
            "awsRegion": "us-east-1"
        }
    }
}
```

### Create a resolver

You can create a resolver using the [`CreateResolver`](https://github.com/localstack/docs/pull/782) API. You can create a custom `request-mapping-template.vtl` and `response-mapping-template.vtl` file to use as a mapping template to use for requests and responses respectively. Execute the following command to create a VTL resolver attached to the `PaginatedNotes.notes` field:

```bash
$ awslocal appsync create-resolver \
    --api-id 014d18d0c2b149ee8b66f39173 \
    --type Query \
    --field PaginatedNotes.notes \
    --data-source-name AppSyncDB \
    --request-mapping-template file://request-mapping-template.vtl \
    --response-mapping-template file://response-mapping-template.vtl
```

## Custom GraphQL API IDs

You can employ a pre-defined ID during the creation of GraphQL APIs by utilizing the special tag `_custom_id_`. For example, the following command will create a GraphQL API with the ID `faceb00c`:

```bash
$ awslocal appsync create-graphql-api \
    --name my-api \
    --authentication-type API_KEY \
    --tags _custom_id_=faceb00c
```

The following output would be retrieved:

```bash
{
    "graphqlApi": {
        "name": "my-api",
        "apiId": "faceb00c",
        "authenticationType": "API_KEY",
        "arn": "arn:aws:appsync:us-east-1:000000000000:apis/my-api",
        "uris": {
            "GRAPHQL": "http://localhost:4566/graphql/faceb00c",
            "REALTIME": "ws://localhost:4510/graphql/faceb00c"
        },
        "tags": {
            "_custom_id_": "faceb00c"
        }
    }
}
```

## GraphQL Data sources

LocalStack supports the following data source types types and services:

| Resolver Type         | Description                                                            |
| --------------------- | ---------------------------------------------------------------------- |
| `AMAZON_DYNAMODB`     | Provides access to DynamoDB tables.                                    |
| `RELATIONAL_DATABASE` | Provides access to RDS database tables.                                |
| `AWS_LAMBDA`          | Allows retrieval of data from Lambda function invocations.             |
| `HTTP`                | Enables calling HTTP endpoints to fetch data.                          |
| `NONE`                | Used for pass-through resolver mapping templates returning input data. |

## GraphQL resolvers

LocalStack's AppSync offers support for both unit and pipeline resolvers, as detailed in the [AWS resolvers documentation](https://docs.aws.amazon.com/appsync/latest/devguide/resolver-components.html). Unit resolvers consist of request and response mapping templates, facilitating the transformation of requests to and from data sources.

Pipeline resolvers, on the other hand, invoke AppSync functions that wraps the AppSync data sources. Unit resolvers are written in the Velocity templating language (VTL), while pipeline resolvers can be written in either VTL or JavaScript.

## Configuring GraphQL Endpoints

There are three configurable strategies that govern how GraphQL API endpoints are created. The strategy can be configured via the `GRAPHQL_ENDPOINT_STRATEGY` environment variable.

| Value    | Format                                                 | Description                                                                                         |
|----------|--------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| `domain` | `<api-id>.appsync-api.localhost.localstack.cloud:4566` | This strategy, slated to be the future default, uses the `localhost.localstack.cloud` domain to route to your localhost. |
| `path`   | `localhost:4566/appsync-api/<api-id>/graphql`         | An alternative strategy that can be beneficial if you're unable to resolve LocalStack's `localhost` domain. |
| `legacy` | `localhost:4566/graphql/<api-id>`                    | This strategy represents the old endpoint format, which is currently the default but will eventually be phased out. |
