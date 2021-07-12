---
title: Benchmark Beagle Movies
weight: 237
description: >-
  You will know more about Beagle Movies on this section, an application developed using Beagle.
---

# Benchmark: Beagle Movies

## Context

Beagle Movies is a movie application developed using Beagle's framework. It is possible to check movies and TV shows reviews and also create customized lists with what you want to watch or what you have already watched.

## What were the tests?

### Payload size

Generally **a [**Server-Driven UI**]({{< ref path="/key-concepts#server-driven-ui" lang="en" >}}) payload is bigger than the native one**, because the whole layout structure comes from the payload, while the native one has only the information to be showed.

Despite that, in some screens the payload is inferior than the server-drive. This happens, because the [**BFF \(Backend For Frontend\)**]({{< ref path="/key-concepts#backend-for-frontend" lang="en" >}}) filters the data that it is not used in the application and it does not send the payload.

| Screens          | Server-Driven screen responses \(in KB\) | Native screens response \(in KB\) |
| :--------------- | :--------------------------------------- | :-------------------------------- |
| Home             | 423,89 KB                                | 300,73 KB                         |
| Detalhes         | 2,15 KB                                  | 3,37 KB                           |
| Search           | 12,8 KB                                  | 14,45 KB                          |
| **Test results** | **438,84 KB**                            | **318,55 KB**                     |

To more complex screens, the server-driven payload will be bigger. It is important to mention that Beagle uses a [cache]({{< ref path="/resources/cache/" lang="en" >}}) mechanism and this test is only considered in the first request.

{{% alert color="info" %}}
The advantage of using Beagle is that **you only develop the screen once**, while in other native screens you will have to develop one for every platform.
{{% /alert %}}

### Response time

- **Native screens:** It is necessary to make several requests to set up the screen.
- **Server-Driven screens:** It is necessary only one BFF request.

| Screens         | Response time on Server-Driven screens \(in ms\) | Response time on native screens \(in ms\) |
| :-------------- | :----------------------------------------------- | :---------------------------------------- |
| Home            | 312 ms                                           | 448 ms                                    |
| Detalhes        | 179 ms                                           | 346 ms                                    |
| Search          | 176 ms                                           | 271 ms                                    |
| **Test result** | **668 ms**                                       | **1065 ms**                               |

## Performance test

### iOS

See the performance difference on native and server-driven screens, considering the initialization process, screen render services request and reponse.

| Phases                        | Server-Driven screen past time \(in seconds\) | Native screens past time \(in seconds\) |
| :---------------------------- | :-------------------------------------------- | :-------------------------------------- |
| Start test                    | 0,00 s                                        | 0,00 s                                  |
| Set up                        | 0,10 s                                        | 0,06 s                                  |
| Open project                  | 0,13 s                                        | 0,08 s                                  |
| Launch app                    | 0,15 s                                        | 0,12 s                                  |
| Setting up automation session | 3,95 s                                        | 2,17 s                                  |
| **Test result**               | **8,21 seconds**                              | **6,39 seconds**                        |

![](/shared/comparativo-ios-v1-1-.gif)
