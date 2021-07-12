---
title: Visão Geral
weight: 1
type: overview
description: >-
  Nesta seção, você encontra a descrição de como criar uma ação customizada e
  detalhes dos métodos que ela implementa.
---

## Introdução

Uma ação \(`Action`\) é um componente do Beagle que pode ser chamado por meio de eventos que são disparados por outros componentes \(inclusive outras ações\).

O Beagle já possui algumas ações pré-definidas, no entanto é possível criar novas ações personalizadas.

{{% alert color="danger" %}}
É **obrigatória** a adição da tag `@Transient` para **todos os atributos** presentes nas classes que representarão as ações para que não sejam levados em conta na serialização e desserialização do componente.
{{% /alert %}}