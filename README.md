# Go SDK for OpenFGA

[![Go Reference](https://pkg.go.dev/badge/github.com/openfga/go-sdk.svg)](https://pkg.go.dev/github.com/openfga/go-sdk)
[![Release](https://img.shields.io/github/v/release/openfga/go-sdk?sort=semver&color=green)](https://github.com/openfga/go-sdk/releases)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](./LICENSE)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fopenfga%2Fgo-sdk.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fopenfga%2Fgo-sdk?ref=badge_shield)
[![Discord Server](https://img.shields.io/discord/759188666072825867?color=7289da&logo=discord "Discord Server")](https://discord.com/channels/759188666072825867/930524706854031421)
[![Twitter](https://img.shields.io/twitter/follow/openfga?color=%23179CF0&logo=twitter&style=flat-square "@openfga on Twitter")](https://twitter.com/openfga)

This is an autogenerated Go SDK for OpenFGA. It provides a wrapper around the [OpenFGA API definition](https://openfga.dev/api).

## Table of Contents

- [About OpenFGA](#about)
- [Resources](#resources)
- [Installation](#installation)
- [Getting Started](#getting-started)
  - [Initializing the API Client](#initializing-the-api-client)
  - [Get your Store ID](#get-your-store-id)
  - [Calling the API](#calling-the-api)
    - [Stores](#stores)
      - [List All Stores](#list-stores)
      - [Create a Store](#create-store)
      - [Get a Store](#get-store)
      - [Delete a Store](#delete-store)
    - [Authorization Models](#authorization-models)
      - [Read Authorization Models](#read-authorization-models)
      - [Write Authorization Model](#write-authorization-model)
      - [Read a Single Authorization Model](#read-a-single-authorization-model)
      - [Read the Latest Authorization Model](#read-the-latest-authorization-model)
    - [Relationship Tuples](#relationship-tuples)
      - [Read Relationship Tuple Changes (Watch)](#read-relationship-tuple-changes-watch)
      - [Read Relationship Tuples](#read-relationship-tuples)
      - [Write (Create and Delete) Relationship Tuples](#write-create-and-delete-relationship-tuples)
    - [Relationship Queries](#relationship-queries)
      - [Check](#check)
      - [Batch Check](#batch-check)
      - [Expand](#expand)
      - [List Objects](#list-objects)
      - [List Relations](#list-relations)
    - [Assertions](#assertions)
      - [Read Assertions](#read-assertions)
      - [Write Assertions](#write-assertions)
  - [API Endpoints](#api-endpoints)
  - [Models](#models)
- [Contributing](#contributing)
  - [Issues](#issues)
  - [Pull Requests](#pull-requests)
- [License](#license)

## About

[OpenFGA](https://openfga.dev) is an open source Fine-Grained Authorization solution inspired by [Google's Zanzibar paper](https://research.google/pubs/pub48190/). It was created by the FGA team at [Auth0](https://auth0.com) based on [Auth0 Fine-Grained Authorization (FGA)](https://fga.dev), available under [a permissive license (Apache-2)](https://github.com/openfga/rfcs/blob/main/LICENSE) and welcomes community contributions.

OpenFGA is designed to make it easy for application builders to model their permission layer, and to add and integrate fine-grained authorization into their applications. OpenFGA’s design is optimized for reliability and low latency at a high scale.


## Resources

- [OpenFGA Documentation](https://openfga.dev/docs)
- [OpenFGA API Documentation](https://openfga.dev/api/service)
- [Twitter](https://twitter.com/openfga)
- [OpenFGA Discord Community](https://discord.gg/8naAwJfWN6)
- [Zanzibar Academy](https://zanzibar.academy)
- [Google's Zanzibar Paper (2019)](https://research.google/pubs/pub48190/)

## Installation

To install:

```
go get -u github.com/openfga/go-sdk
```

In your code, import the module and use it:

```go
import "github.com/openfga/go-sdk"

func Main() {
	configuration, err := openfga.NewConfiguration(openfga.Configuration{})
}
```

You can then run

```shell
go mod tidy
```

to update `go.mod` and `go.sum` if you are using them.


## Getting Started

### Initializing the API Client

[Learn how to initialize your SDK](https://openfga.dev/docs/getting-started/setup-sdk-client)

#### No Credentials

```golang
import (
    . "github.com/openfga/go-sdk/client"
    "os"
)

func main() {
    fgaClient, err := NewSdkClient(&ClientConfiguration{
        ApiScheme:      os.Getenv("OPENFGA_API_SCHEME"), // optional, defaults to "https"
        ApiHost:        os.Getenv("OPENFGA_API_HOST"), // required, define without the scheme (e.g. api.fga.example instead of https://api.fga.example)
        StoreId:        os.Getenv("OPENFGA_STORE_ID"), // not needed when calling `CreateStore` or `ListStores`
	  })
    
	  if err != nil {
    // .. Handle error
    }
}
```

#### API Token

```golang
import (
    . "github.com/openfga/go-sdk/client"
    "os"
)

func main() {
    fgaClient, err := NewSdkClient(&ClientConfiguration{
        ApiScheme:      os.Getenv("OPENFGA_API_SCHEME"), // optional, defaults to "https"
        ApiHost:        os.Getenv("OPENFGA_API_HOST"), // required, define without the scheme (e.g. api.fga.example instead of https://api.fga.example)
        StoreId:        os.Getenv("OPENFGA_STORE_ID"), // not needed when calling `CreateStore` or `ListStores`
        Credentials: &credentials.Credentials{
            Method: credentials.CredentialsMethodApiToken,
            Config: &credentials.Config{
                ApiToken: os.Getenv("OPENFGA_API_TOKEN"), // will be passed as the "Authorization: Bearer ${ApiToken}" request header
            },
        },
	  })

    if err != nil {
    // .. Handle error
    }
}
```

#### Client Credentials

```golang
import (
    . "github.com/openfga/go-sdk/client"
    "os"
)

func main() {
    fgaClient, err := NewSdkClient(&ClientConfiguration{
        ApiScheme:      os.Getenv("OPENFGA_API_SCHEME"), // optional, defaults to "https"
        ApiHost:        os.Getenv("OPENFGA_API_HOST"), // required, define without the scheme (e.g. api.fga.example instead of https://api.fga.example)
        StoreId:        os.Getenv("OPENFGA_STORE_ID"), // not needed when calling `CreateStore` or `ListStores`
        Credentials: &credentials.Credentials{
            Method: credentials.CredentialsMethodClientCredentials,
            Config: &credentials.Config{
                ClientCredentialsClientId:       os.Getenv("OPENFGA_CLIENT_ID"),
                ClientCredentialsClientSecret:   os.Getenv("OPENFGA_CLIENT_SECRET"),
                ClientCredentialsApiAudience:    os.Getenv("OPENFGA_API_AUDIENCE"),
                ClientCredentialsApiTokenIssuer: os.Getenv("OPENFGA_API_TOKEN_ISSUER"),
            },
        },
	  })

    if err != nil {
    // .. Handle error
    }
}
```


### Get your Store ID

You need your store id to call the OpenFGA API (unless it is to call the [CreateStore](#create-store) or [ListStores](#list-stores) methods).

If your server is configured with [authentication enabled](https://openfga.dev/docs/getting-started/setup-openfga#configuring-authentication), you also need to have your credentials ready.

### Calling the API

#### Stores

##### List Stores

Get a paginated list of stores.

[API Documentation](https://openfga.dev/api/service/docs/api#/Stores/ListStores)

```golang
options := ClientListStoresOptions{
  PageSize:          openfga.PtrInt32(10),
  ContinuationToken: openfga.PtrString("..."),
}
stores, httpResponse, err := fgaClient.ListStores(context.Background()).Options(options).Execute();

// stores = [{ "id": "01FQH7V8BEG3GPQW93KTRFR8JB", "name": "FGA Demo Store", "created_at": "2022-01-01T00:00:00.000Z", "updated_at": "2022-01-01T00:00:00.000Z" }]
```

##### Create Store

Initialize a store.

[API Documentation](https://openfga.dev/api/service/docs/api#/Stores/CreateStore)

```golang
body := ClientCreateStoreRequest{Name: "FGA Demo"}
store, httpResponse, err := fgaClient.CreateStore(context.Background()).Body(body).Execute()
if err != nil {
    // handle error
}

// store.Id = "01FQH7V8BEG3GPQW93KTRFR8JB"

// store store.Id in database
// update the storeId of the current instance
apiClient.SetStoreId(*store.Id)
// continue calling the API normally, scoped to this store
```

##### Get Store

[API Documentation](https://openfga.dev/api/service/docs/api#/Stores/GetStore)

> Requires a client initialized with a storeId

```golang
store, httpResponse, err := fgaClient.GetStore(context.Background()).Execute()
if err != nil {
    // handle error
}

// store = { "id": "01FQH7V8BEG3GPQW93KTRFR8JB", "name": "FGA Demo Store", "created_at": "2022-01-01T00:00:00.000Z", "updated_at": "2022-01-01T00:00:00.000Z" }
```

##### Delete Store

[API Documentation](https://openfga.dev/api/service/docs/api#/Stores/DeleteStore)

> Requires a client initialized with a storeId

```golang
_, httpResponse, err := fgaClient.DeleteStore(context.Background()).Execute()
if err != nil {
    // handle error
}
```
#### Authorization Models

##### Read Authorization Models

Read all authorization models in the store.

[API Documentation](https://openfga.dev/api/service#/Authorization%20Models/ReadAuthorizationModels)

```golang
options := ClientReadAuthorizationModelsOptions{
    PageSize: openfga.PtrInt32(10),
    ContinuationToken: openfga.PtrString("..."),
}
data, err := fgaClient.ReadAuthorizationModels(context.Background()).Options(options).Execute()

// data.AuthorizationModels = [
// { Id: "01GXSA8YR785C4FYS3C0RTG7B1", SchemaVersion: "1.1", TypeDefinitions: [...] },
// { Id: "01GXSBM5PVYHCJNRNKXMB4QZTW", SchemaVersion: "1.1", TypeDefinitions: [...] }];
```

##### Write Authorization Model

Create a new authorization model.

[API Documentation](https://openfga.dev/api/service#/Authorization%20Models/WriteAuthorizationModel)

> Note: To learn how to build your authorization model, check the Docs at https://openfga.dev/docs.

> Learn more about [the OpenFGA configuration language](https://openfga.dev/docs/configuration-language).

> You can use the [OpenFGA Syntax Transformer](https://github.com/openfga/syntax-transformer) to convert between the friendly DSL and the JSON authorization model.

```golang
body := ClientWriteAuthorizationModelRequest{
  SchemaVersion: "1.1",
  TypeDefinitions: []openfga.TypeDefinition{
    {Type: "user", Relations: &map[string]openfga.Userset{}},
    {
      Type: "document",
      Relations: &map[string]openfga.Userset{
        "writer": {
          This: &map[string]interface{}{},
        },
        "viewer": {Union: &openfga.Usersets{
          Child: &[]openfga.Userset{
            {This: &map[string]interface{}{}},
            {ComputedUserset: &openfga.ObjectRelation{
              Object:   openfga.PtrString(""),
              Relation: openfga.PtrString("writer"),
            }},
          },
        }},
      },
      Metadata: &openfga.Metadata{
        Relations: &map[string]openfga.RelationMetadata{
          "writer": {
            DirectlyRelatedUserTypes: &[]openfga.RelationReference{
              {Type: "user"},
            },
          },
          "viewer": {
            DirectlyRelatedUserTypes: &[]openfga.RelationReference{
              {Type: "user"},
            },
          },
        },
      },
    }},
}
data, err := fgaClient.WriteAuthorizationModel(context.Background()).Body(body).Execute()

fmt.Printf("%s", data.AuthorizationModelId) // 01GXSA8YR785C4FYS3C0RTG7B1
```

##### Read a Single Authorization Model

Read a particular authorization model.

[API Documentation](https://openfga.dev/api/service#/Authorization%20Models/ReadAuthorizationModel)

```golang
options := ClientReadAuthorizationModelOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString(modelId),
}
data, err := fgaClient.ReadAuthorizationModel(context.Background()).Options(options).Execute()

// data = {"authorization_model":{"id":"01GXSA8YR785C4FYS3C0RTG7B1","schema_version":"1.1","type_definitions":[{"type":"document","relations":{"writer":{"this":{}},"viewer":{ ... }}},{"type":"user"}]}} // JSON

fmt.Printf("%s", data.AuthorizationModel.Id) // 01GXSA8YR785C4FYS3C0RTG7B1
```

##### Read the Latest Authorization Model

Reads the latest authorization model (note: this ignores the model id in configuration).

[API Documentation](https://openfga.dev/api/service#/Authorization%20Models/ReadAuthorizationModel)

```golang
data, err := fgaClient.ReadLatestAuthorizationModel(context.Background()).Execute()

// data.AuthorizationModel.Id = "01GXSA8YR785C4FYS3C0RTG7B1"
// data.AuthorizationModel.SchemaVersion = "1.1"
// data.AuthorizationModel.TypeDefinitions = [{ "type": "document", "relations": { ... } }, { "type": "user", "relations": { ... }}]

fmt.Printf("%s", (*data.AuthorizationModel).GetId()) // 01GXSA8YR785C4FYS3C0RTG7B1
```

#### Relationship Tuples

##### Read Relationship Tuple Changes (Watch)

Reads the list of historical relationship tuple writes and deletes.

[API Documentation](https://openfga.dev/api/service#/Relationship%20Tuples/ReadChanges)

```golang
body := ClientReadChangesRequest{
    Type: "document",
}
options := ClientReadChangesOptions{
    PageSize: openfga.PtrInt32(10),
    ContinuationToken: openfga.PtrString("eyJwayI6IkxBVEVTVF9OU0NPTkZJR19hdXRoMHN0b3JlIiwic2siOiIxem1qbXF3MWZLZExTcUoyN01MdTdqTjh0cWgifQ=="),
}
data, err := fgaClient.ReadChanges(context.Background()).Body(body).Options(options).Execute()

// data.ContinuationToken = ...
// data.Changes = [
//   { TupleKey: { User, Relation, Object }, Operation: TupleOperation.WRITE, Timestamp: ... },
//   { TupleKey: { User, Relation, Object }, Operation: TupleOperation.DELETE, Timestamp: ... }
// ]
```

##### Read Relationship Tuples

Reads the relationship tuples stored in the database. It does not evaluate nor exclude invalid tuples according to the authorization model.

[API Documentation](https://openfga.dev/api/service#/Relationship%20Tuples/Read)

```golang
// Find if a relationship tuple stating that a certain user is a viewer of a certain document
body := ClientReadRequest{
    User:     openfga.PtrString("user:81684243-9356-4421-8fbf-a4f8d36aa31b"),
    Relation: openfga.PtrString("viewer"),
    Object:   openfga.PtrString("document:roadmap"),
}

// Find all relationship tuples where a certain user has a relationship as any relation to a certain document
body := ClientReadRequest{
    User:     openfga.PtrString("user:81684243-9356-4421-8fbf-a4f8d36aa31b"),
    Object:   openfga.PtrString("document:roadmap"),
}

// Find all relationship tuples where a certain user is a viewer of any document
body := ClientReadRequest{
    User:     openfga.PtrString("user:81684243-9356-4421-8fbf-a4f8d36aa31b"),
    Relation: openfga.PtrString("viewer"),
    Object:   openfga.PtrString("document:"),
}

// Find all relationship tuples where any user has a relationship as any relation with a particular document
body := ClientReadRequest{
    Object:   openfga.PtrString("document:roadmap"),
}

// Read all stored relationship tuples
body := ClientReadRequest{}

options := ClientReadOptions{
    PageSize: openfga.PtrInt32(10),
    ContinuationToken: openfga.PtrString("eyJwayI6IkxBVEVTVF9OU0NPTkZJR19hdXRoMHN0b3JlIiwic2siOiIxem1qbXF3MWZLZExTcUoyN01MdTdqTjh0cWgifQ=="),
}
data, err := fgaClient.Read(context.Background()).Body(requestBody).Options(options).Execute()

// In all the above situations, the response will be of the form:
// data = { Tuples: [{ Key: { User, Relation, Object }, Timestamp }, ...]}
```

##### Write (Create and Delete) Relationship Tuples

Create and/or delete relationship tuples to update the system state.

[API Documentation](https://openfga.dev/api/service#/Relationship%20Tuples/Write)

###### Transaction mode (default)

By default, write runs in a transaction mode where any invalid operation (deleting a non-existing tuple, creating an existing tuple, one of the tuples was invalid) or a server error will fail the entire operation.

```golang
body := ClientWriteRequest{
    Writes: &[]ClientTupleKey{{
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "viewer",
        Object:   "document:roadmap",
    }, {
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "viewer",
        Object:   "document:budget",
    }},
    Deletes: &[]ClientTupleKey{{
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "writer",
        Object:   "document:roadmap",
    }}
}

options := ClientWriteOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
}
httpResponse, err := fgaClient.Write(context.Background()).Body(requestBody).Options(options).Execute()
```

Convenience `WriteTuples` and `DeleteTuples` methods are also available.

###### Non-transaction mode

The SDK will split the writes into separate chunks and send them in separate requests. Each chunk is a transaction. By default, each chunk is set to 1, but you may override that.

```golang
body := ClientWriteRequest{
    Writes: &[]ClientTupleKey{{
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "viewer",
        Object:   "document:roadmap",
    }, {
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "viewer",
        Object:   "document:budget",
    }},
	  Deletes: &[]ClientTupleKey{{
      User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
      Relation: "writer",
      Object:   "document:roadmap",
    }}
}

options := ClientWriteOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
    Transaction: &TransactionOptions{
        Disable: true,
        MaxParallelRequests: 5, // Maximum number of requests to issue in parallel
        MaxPerChunk: 1, // Maximum number of requests to be sent in a transaction in a particular chunk
    },
}
data, err := fgaClient.Write(context.Background()).Body(requestBody).Options(options).Execute()

// data.Writes = [{
//   TupleKey: { User, Relation, Object },
//   Status: "CLIENT_WRITE_STATUS_SUCCESS
//   HttpResponse: ... // http response"
// }, {
//   TupleKey: { User, Relation, Object },
//   Status: "CLIENT_WRITE_STATUS_FAILURE
//   HttpResponse: ... // http response"
//   Error: ...
// }]
// data.Deletes = [{
//   TupleKey: { User, Relation, Object },
//   Status: "CLIENT_WRITE_STATUS_SUCCESS
//   HttpResponse: ... // http response"
// }]
```

#### Relationship Queries

##### Check

Check if a user has a particular relation with an object.

[API Documentation](https://openfga.dev/api/service#/Relationship%20Queries/Check)

> Provide a tuple and ask the OpenFGA API to check for a relationship

```golang
body := ClientCheckRequest{
    User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "viewer",
    Object:   "document:roadmap",
    ContextualTuples: &[]ClientTupleKey{{
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "editor",
        Object:   "document:roadmap",
    }},
}

options := ClientCheckOptions{
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
}
data, err := fgaClient.Check(context.Background()).Body(body).Options(options).Execute()

// data = {"allowed":true,"resolution":""} // in JSON

fmt.Printf("%t", data.GetAllowed()) // True

```

##### Batch Check

Run a set of [checks](#check). Batch Check will return `allowed: false` if it encounters an error, and will return the error in the body.
If 429s or 5xxs are encountered, the underlying check will retry up to 15 times before giving up.

```golang
options := ClientBatchCheckOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
    MaxParallelRequests: openfga.PtrInt32(5), // Max number of requests to issue in parallel, defaults to 10
}

body := ClientBatchCheckBody{{
    User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "viewer",
    Object:   "document:roadmap",
    ContextualTuples: &[]ClientTupleKey{{
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "editor",
        Object:   "document:roadmap",
    }},
}, {
    User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "admin",
    Object:   "document:roadmap",
    ContextualTuples: &[]ClientTupleKey{{
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "editor",
        Object:   "document:roadmap",
    }},
}, {
    User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "creator",
    Object:   "document:roadmap",
}, {
    User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "deleter",
    Object:   "document:roadmap",
}}

data, err := fgaClient.BatchCheck(context.Background()).Body(requestBody).Options(options).Execute()

/*
data = [{
  Allowed: false,
  Request: {
    User: "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "viewer",
    Object: "document:roadmap",
    ContextualTuples: [{
      User: "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
      Relation: "editor",
      Object: "document:roadmap"
    }]
  },
  HttpResponse: ...
}, {
  Allowed: false,
  Request: {
    User: "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "admin",
    Object: "document:roadmap",
    ContextualTuples: [{
      User: "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
      Relation: "editor",
      Object: "document:roadmap"
    }]
  },
  HttpResponse: ...
}, {
  Allowed: false,
  Request: {
    User: "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "creator",
    Object: "document:roadmap",
  },
  HttpResponse: ...,
  Error: <FgaError ...>
}, {
  Allowed: true,
  Request: {
    User: "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "deleter",
    Object: "document:roadmap",
  }},
  HttpResponse: ...,
]
*/
```

##### Expand

Expands the relationships in userset tree format.

[API Documentation](https://openfga.dev/api/service#/Relationship%20Queries/Expand)

```golang
options := ClientExpandOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
}
body := ClientExpandRequest{
    Relation: "viewer",
    Object:   "document:roadmap",
}
data, err := fgaClient.Expand(context.Background()).Body(requestBody).Options(options).Execute()

// data.Tree.Root = {"name":"document:roadmap#viewer","leaf":{"users":{"users":["user:81684243-9356-4421-8fbf-a4f8d36aa31b","user:f52a4f7a-054d-47ff-bb6e-3ac81269988f"]}}}
```

#### List Objects

List the objects of a particular type a user has access to.

[API Documentation](https://openfga.dev/api/service#/Relationship%20Queries/ListObjects)

```golang
options := ClientListObjectsOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
}
body := ClientListObjectsRequest{
    User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Relation: "can_read",
    Type:     "document",
    ContextualTuples: &[]ClientTupleKey{{
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "editor",
        Object:   "folder:product",
    }, {
        User:     "folder:product",
        Relation: "parent",
        Object:   "document:roadmap",
    }},
}
data, err := fgaClient.ListObjects(context.Background()).
  Body(requestBody).
  Options(options).
  Execute()

// data.Objects = ["document:roadmap"]
```

#### List Relations

List the relations a user has on an object.

```golang
options := ClientListRelationsOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
}
body := ClientListRelationsRequest{
    User:      "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
    Object:    "document:roadmap",
    Relations: []string{"can_view", "can_edit", "can_delete", "can_rename"},
    ContextualTuples: &[]ClientTupleKey{{
        User:     "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation: "editor",
        Object:   "document:roadmap",
    }},
}
data, err := fgaClient.ListRelations(context.Background()).
  Body(requestBody).
  Options(options).
  Execute()

// data.Relations = ["can_view", "can_edit"]
```

### Assertions

#### Read Assertions

Read assertions for a particular authorization model.

[API Documentation](https://openfga.dev/api/service#/Assertions/Read%20Assertions)

```golang
options := ClientReadAssertionsOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
}
data, err := fgaClient.ReadAssertions(context.Background()).
  Options(options).
  Execute()
```

#### Write Assertions

Update the assertions for a particular authorization model.

[API Documentation](https://openfga.dev/api/service#/Assertions/Write%20Assertions)

```golang
options := ClientWriteAssertionsOptions{
    // You can rely on the model id set in the configuration or override it for this specific request
    AuthorizationModelId: openfga.PtrString("01GAHCE4YVKPQEKZQHT2R89MQV"),
}
requestBody := ClientWriteAssertionsRequest{
    ClientAssertion{
        User:        "user:81684243-9356-4421-8fbf-a4f8d36aa31b",
        Relation:    "can_view",
        Object:      "document:roadmap",
        Expectation: true,
    },
}
data, err := fgaClient.WriteAssertions(context.Background()).
  Body(requestBody).
  Options(options).
  Execute()
```


### API Endpoints

Class | Method | HTTP request | Description
------------ | ------------- | ------------- | -------------
*OpenFgaApi* | [**Check**](docs/OpenFgaApi.md#check) | **Post** /stores/{store_id}/check | Check whether a user is authorized to access an object
*OpenFgaApi* | [**CreateStore**](docs/OpenFgaApi.md#createstore) | **Post** /stores | Create a store
*OpenFgaApi* | [**DeleteStore**](docs/OpenFgaApi.md#deletestore) | **Delete** /stores/{store_id} | Delete a store
*OpenFgaApi* | [**Expand**](docs/OpenFgaApi.md#expand) | **Post** /stores/{store_id}/expand | Expand all relationships in userset tree format, and following userset rewrite rules.  Useful to reason about and debug a certain relationship
*OpenFgaApi* | [**GetStore**](docs/OpenFgaApi.md#getstore) | **Get** /stores/{store_id} | Get a store
*OpenFgaApi* | [**ListObjects**](docs/OpenFgaApi.md#listobjects) | **Post** /stores/{store_id}/list-objects | Get all objects of the given type that the user has a relation with
*OpenFgaApi* | [**ListStores**](docs/OpenFgaApi.md#liststores) | **Get** /stores | List all stores
*OpenFgaApi* | [**Read**](docs/OpenFgaApi.md#read) | **Post** /stores/{store_id}/read | Get tuples from the store that matches a query, without following userset rewrite rules
*OpenFgaApi* | [**ReadAssertions**](docs/OpenFgaApi.md#readassertions) | **Get** /stores/{store_id}/assertions/{authorization_model_id} | Read assertions for an authorization model ID
*OpenFgaApi* | [**ReadAuthorizationModel**](docs/OpenFgaApi.md#readauthorizationmodel) | **Get** /stores/{store_id}/authorization-models/{id} | Return a particular version of an authorization model
*OpenFgaApi* | [**ReadAuthorizationModels**](docs/OpenFgaApi.md#readauthorizationmodels) | **Get** /stores/{store_id}/authorization-models | Return all the authorization models for a particular store
*OpenFgaApi* | [**ReadChanges**](docs/OpenFgaApi.md#readchanges) | **Get** /stores/{store_id}/changes | Return a list of all the tuple changes
*OpenFgaApi* | [**Write**](docs/OpenFgaApi.md#write) | **Post** /stores/{store_id}/write | Add or delete tuples from the store
*OpenFgaApi* | [**WriteAssertions**](docs/OpenFgaApi.md#writeassertions) | **Put** /stores/{store_id}/assertions/{authorization_model_id} | Upsert assertions for an authorization model ID
*OpenFgaApi* | [**WriteAuthorizationModel**](docs/OpenFgaApi.md#writeauthorizationmodel) | **Post** /stores/{store_id}/authorization-models | Create a new authorization model


### Models

 - [Any](docs/Any.md)
 - [Assertion](docs/Assertion.md)
 - [AuthorizationModel](docs/AuthorizationModel.md)
 - [CheckRequest](docs/CheckRequest.md)
 - [CheckResponse](docs/CheckResponse.md)
 - [Computed](docs/Computed.md)
 - [ContextualTupleKeys](docs/ContextualTupleKeys.md)
 - [CreateStoreRequest](docs/CreateStoreRequest.md)
 - [CreateStoreResponse](docs/CreateStoreResponse.md)
 - [Difference](docs/Difference.md)
 - [ErrorCode](docs/ErrorCode.md)
 - [ExpandRequest](docs/ExpandRequest.md)
 - [ExpandResponse](docs/ExpandResponse.md)
 - [GetStoreResponse](docs/GetStoreResponse.md)
 - [InternalErrorCode](docs/InternalErrorCode.md)
 - [InternalErrorMessageResponse](docs/InternalErrorMessageResponse.md)
 - [Leaf](docs/Leaf.md)
 - [ListObjectsRequest](docs/ListObjectsRequest.md)
 - [ListObjectsResponse](docs/ListObjectsResponse.md)
 - [ListStoresResponse](docs/ListStoresResponse.md)
 - [Metadata](docs/Metadata.md)
 - [Node](docs/Node.md)
 - [Nodes](docs/Nodes.md)
 - [NotFoundErrorCode](docs/NotFoundErrorCode.md)
 - [ObjectRelation](docs/ObjectRelation.md)
 - [PathUnknownErrorMessageResponse](docs/PathUnknownErrorMessageResponse.md)
 - [ReadAssertionsResponse](docs/ReadAssertionsResponse.md)
 - [ReadAuthorizationModelResponse](docs/ReadAuthorizationModelResponse.md)
 - [ReadAuthorizationModelsResponse](docs/ReadAuthorizationModelsResponse.md)
 - [ReadChangesResponse](docs/ReadChangesResponse.md)
 - [ReadRequest](docs/ReadRequest.md)
 - [ReadResponse](docs/ReadResponse.md)
 - [RelationMetadata](docs/RelationMetadata.md)
 - [RelationReference](docs/RelationReference.md)
 - [Status](docs/Status.md)
 - [Store](docs/Store.md)
 - [Tuple](docs/Tuple.md)
 - [TupleChange](docs/TupleChange.md)
 - [TupleKey](docs/TupleKey.md)
 - [TupleKeys](docs/TupleKeys.md)
 - [TupleOperation](docs/TupleOperation.md)
 - [TupleToUserset](docs/TupleToUserset.md)
 - [TypeDefinition](docs/TypeDefinition.md)
 - [Users](docs/Users.md)
 - [Userset](docs/Userset.md)
 - [UsersetTree](docs/UsersetTree.md)
 - [UsersetTreeDifference](docs/UsersetTreeDifference.md)
 - [UsersetTreeTupleToUserset](docs/UsersetTreeTupleToUserset.md)
 - [Usersets](docs/Usersets.md)
 - [ValidationErrorMessageResponse](docs/ValidationErrorMessageResponse.md)
 - [WriteAssertionsRequest](docs/WriteAssertionsRequest.md)
 - [WriteAuthorizationModelRequest](docs/WriteAuthorizationModelRequest.md)
 - [WriteAuthorizationModelResponse](docs/WriteAuthorizationModelResponse.md)
 - [WriteRequest](docs/WriteRequest.md)


## Contributing

### Issues

If you have found a bug or if you have a feature request, please report them on the [sdk-generator repo](https://github.com/openfga/sdk-generator/issues) issues section. Please do not report security vulnerabilities on the public GitHub issue tracker.

### Pull Requests

All changes made to this repo will be overwritten on the next generation, so we kindly ask that you send all pull requests related to the SDKs to the [sdk-generator repo](https://github.com/openfga/sdk-generator) instead.

## Author

[OpenFGA](https://github.com/openfga)

## License

This project is licensed under the Apache-2.0 license. See the [LICENSE](https://github.com/openfga/go-sdk/blob/main/LICENSE) file for more info.

The code in this repo was auto generated by [OpenAPI Generator](https://github.com/OpenAPITools/openapi-generator) from a template based on the [go template](https://github.com/OpenAPITools/openapi-generator/tree/master/modules/openapi-generator/src/main/resources/go), licensed under the [Apache License 2.0](https://github.com/OpenAPITools/openapi-generator/blob/master/LICENSE).

This repo bundles some code from the [golang.org/x/oauth2](https://pkg.go.dev/golang.org/x/oauth2) package. You can find the code [here](./oauth2) and corresponding [BSD-3 License](./oauth2/LICENSE).
