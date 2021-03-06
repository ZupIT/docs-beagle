---
title: "Beagle gRPC for Web"
weight: 1
description: >-
  In this section, you will find information about the client and the CLI of gRPC for Beagle Web (Angular and React).
---

## What is gRPC?

---

It is an open source framework to perform RPC (Remote call procedure) calls, through the HTTP2 protocol in a two-way streaming format, using Protobufs as interfaces between client and server.

- **Protobufs or Protocol Buffers**: They are structured data serialization mechanisms created by Google, which aim to keep contracts always valid and consistent, regarding the implementations that consume a gRPC service.

- **Proto files**: They are very similar to any interface we are used to on the daily basis, in their content. However, they have the .proto extension, where you can define which objects and methods are likely to be used in the service's communication.

[Learn more...](https://grpc.io/)

## gRPC for Beagle Web

---

gRPC is a technology that uses the **HTTP 2** protocol, however, this technology is not compatible with the current Web structure. So, the new structure created uses a Client and a CLI: 
- The CLI is responsible for handling gRPC connections through a Proxy written with Go
- The Client is responsible for handling all requests made by Beagle to the Proxy.

## Requirements

---

- [Git - CLI](https://git-scm.com/): Used internally as a proxy dependency.
- [Go - CLI](https://golang.org/): Language which the proxy is written.
- [Dep - CLI](https://github.com/golang/dep): Go's dependency manager, where it checks that everything is installed and working as expected.
- [Protoc - CLI](https://github.com/protocolbuffers/protobuf): Used by protobufs and necessary for code generation through a `.proto` file.

## Installation

---

Install the library globally. For a better experience with the CLI, install it in your project using the following commands:

```bash
npm install -g @zup-it/beagle-web-grpc
npm install --save-dev @zup-it/beagle-web-grpc
```

or if you use yarn:

```bash
yarn global add @zup-it/beagle-web-grpc
yarn add --dev @zup-it/beagle-web-grpc
```

## Getting Started

---

To start using, follow the next steps: 
**Step 1.** After installing the library in the project and globally, create the configuration file in the project's root folder, using: **`beagle-web-grpc init`** and then configure the attributes according to your needs.
**Step 2.**  Import the gRPC Client, check out below: 

   - **React:** ./src/beagle/beagle-service.ts

     ```typescript
     import { createBeagleUIService } from "@zup-it/beagle-react";
     import { usingBeagleGrpcClient } from "@zup-it/beagle-web-grpc";

     export default createBeagleUIService({
       baseUrl: "grpc://",
       components: {},
       fetchData: usingBeagleGrpcClient({
         proxyAddress: "http://localhost:8081",
       }),
     });
     ```

   - **Angular:** ./src/app/beagle.module.ts

     ```typescript
     import { BeagleModule } from "@zup-it/beagle-angular";
     import { usingBeagleGrpcClient } from "@zup-it/beagle-web-grpc";

     @BeagleModule({
       baseUrl: "grpc://",
       module: {
         path: "./beagle-components.module",
         name: "BeagleComponentsModule",
       },
       components: {},
       fetchData: usingBeagleGrpcClient({
         proxyAddress: "http://localhost:8081",
       }),
     })
     export class Beagle {}
     ```

**Step 3.** Start your proxy running the following command on the root of the project: **`beagle-web-grpc start-proxy`** or **`beagle-web-grpc spx`** or **`beagle-web-grpc start-proxy --mode your-mode`.**
**Step 4.** Use the Beagle Web as usually.

> **Observations**
> Make sure that your BFF Application is running.

## CLI

---

gRPC is not compatible with the current structure of the Web, to support the technology, you have to use a Proxy to establish the communication between the REST application and the gRPC Backend application. 
Beagle team created a CLI (command-line interface), **beagle-web-grpc**, to make it easier to use the proxy and to provide all the necessary settings.

#### Available commands for **beagle-web-grpc**

| Command       | Alias | Options                                                                                                     | Description                                                                                                                                                                                                                     |
| ------------- | ----- | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `init`        | `i`   |                                                                                                             | Creates the configuration file which define the settings to be used by the Beagle Web gRPC Proxy.                                                                                                                               |
| `start-proxy` | `spx` | _-m, --mode <mode>_, set the mode to be used on the configuration file, the default mode is **development** | Start the gRPC Proxy service, on the port defined in the configuration file, to handle requests between your gRPC server and your application using Beagle Web Frontend (IMPORTANT: You must have all requirements installed) . |

#### Configuration file _./beagle-grpc.config.json_

After running `beagle-web-grpc init` the file _./beagle-grpc.config.json_ will be created where the command was executed, generating a file with this content:

```json
{
  "configs": [
    {
      "mode": "development",
      "grpcBackendAddress": "localhost:50051",
      "tlsCertificatePath": "",
      "tlsKeyPath": "",
      "runProxyOnPort": 8081
    },
    {
      "mode": "production",
      "grpcBackendAddress": "https://my-grpc.backend.com/address",
      "tlsCertificatePath": "",
      "tlsKeyPath": "",
      "runProxyOnPort": 8081
    }
  ]
}
```

##### Attributes of the configuration file _./beagle-grpc.config.json_

| Attribute            | Description                                                                                                                                                 |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `mode`               | Defines the mode name for the settings to be used when running the gRPC Proxy                                                                               |
| `grpcBackendAddress` | Defines the address of the backend server that communicates using gRPC. Remembering that all requests that match this route will be redirected to the Proxy |
| `tlsCertificatePath` | Set path to TLS Certificate location                                                                                                                        |
| `tlsKeyPath`         | Set path to TLS Key location                                                                                                                                |
| `runProxyOnPort`     | Defines the Port on which the gRPC Proxy will run                                                                                                           |

## Client

---

The gRPC Client for Beagle Web is what redirects gRPC requests to the proxy. The client is used through `BeagleService` in React and `Beagle`'s module in Angular. When using the `fetchData` property, Beagle allows you to create custom HTTP requests, so it goes like this:

```typescript
import { usingBeagleGrpcClient } from "@zup-it/beagle-web-grpc";

// { ... } - your module or service code
{
  // { ... } - other attributes
  fetchData: usingBeagleGrpcClient({ proxyAddress: "http://localhost:8081" });
}
```

The gRPC client has the following initialization options (`BeagleGrpcClientOptions`):

| Attribute          | Required? | Default Value | Description                                                                                                                                                             |
| ------------------ | --------- | ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `proxyAddress`     | Yes       |               | Address where the Proxy will be running for the client to communicate with the proxy                                                                                    |
| `redirectGrpcFrom` | No        | `grpc://`     | If this attribute is informed, all requests that the url starts with the informed value will be redirected to the gRPC Proxy, otherwise they will be forwarded normally |
| `customHttpClient` | No        | `undefined`   | If this attribute is informed, it will still work as a function to perform custom HTTP requests, however, only if the condition of being a gRPC request is not met      |
