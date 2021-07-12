---
title: Benchmark Beagle Movies
weight: 237
description: >-
  Nesta seção, você irá conhecer mais sobre o Beagle Movies, aplicativo
  desenvolvido usando o Beagle.
---

# Benchmark: Beagle Movies

## Contexto

O Beagle Movies é um aplicativo de filmes que foi desenvolvido usando o framework do Beagle. Com ele, é possível conferir resenhas de filmes e séries, além de criar listas personalizadas com o que você quer ou já assistiu.

## Quais foram os testes?

### Tamanho do Payload

De modo geral, **é esperado que o payload do** [**Server-Driven UI**]({{< ref path="/key-concepts#server-driven-ui" lang="pt" >}}) **seja maior que o nativo**, pois toda estrutura de layout vem no payload, enquanto o nativo somente as informações a serem mostradas.

Apesar disso, em algumas telas o tamanho do payload é inferior ao server driven. Isso acontece porque [**BFF \(Backend For Frontend\)**]({{< ref path="/key-concepts#backend-for-frontend" lang="pt" >}}) filtra os dados que não são usados no aplicativo e não envia no payload.

| Telas                  | Resposta em telas Server-Driven \(em KB\) | Resposta em telas nativas \(em KB\) |
| :--------------------- | :---------------------------------------- | :---------------------------------- |
| Home                   | 423,89 KB                                 | 300,73 KB                           |
| Detalhes               | 2,15 KB                                   | 3,37 KB                             |
| Search                 | 12,8 KB                                   | 14,45 KB                            |
| **Resultado do teste** | **438,84 KB**                             | **318,55 KB**                       |

Para telas mais complexas, o payload do server-driven ficará maior. É importante ressaltar que o Beagle utiliza um mecanismo de [**cache**]({{< ref path="/resources/cache/" lang="pt" >}}) e que esse teste é considerado apenas a primeira requisição.

{{% alert color="info" %}}
A vantagem em usar o Beagle é porque **você só desenvolve a tela apenas uma vez**, enquant em telas nativas você terá de desenvolver para cada plataforma.
{{% /alert %}}

### Tempo de Resposta

- **Telas Nativas:** É necessário fazer várias requisições para montar a tela
- **Telas Server-Driven:** É necessário apenas uma requisição do BFF

| Telas                  | Tempo de resposta em telas Server-Driven \(em ms\) | Tempo de resposta em telas nativas \(em ms\) |
| :--------------------- | :------------------------------------------------- | :------------------------------------------- |
| Home                   | 312 ms                                             | 448 ms                                       |
| Detalhes               | 179 ms                                             | 346 ms                                       |
| Search                 | 176 ms                                             | 271 ms                                       |
| **Resultado do teste** | **668 ms**                                         | **1065 ms**                                  |

## Teste de performance

### iOS

Veja a diferença de performance em telas nativas e server-driven considerando o processo de inicialização, chamada e resposta dos serviços de renderização da tela.

| Etapas                        | Tempo decorrido em telas Server-Driven \(em segundos\) | Tempo decorrido em telas nativas \(em segundos\) |
| :---------------------------- | :----------------------------------------------------- | :----------------------------------------------- |
| Start test                    | 0,00 s                                                 | 0,00 s                                           |
| Set up                        | 0,10 s                                                 | 0,06 s                                           |
| Open project                  | 0,13 s                                                 | 0,08 s                                           |
| Launch app                    | 0,15 s                                                 | 0,12 s                                           |
| Setting up automation session | 3,95 s                                                 | 2,17 s                                           |
| **Resultado do teste**        | **8,21 segundos**                                      | **6,39 segundos**                                |

![](/shared/comparativo-ios-v1-1-.gif)
