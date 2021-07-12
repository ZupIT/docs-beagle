---
title: Widgets customizados
weight: 129
description: >-
  Nesta seção, você encontra um exemplo de com criar um componente e um widget
  customizado.
---

---

## Introdução

O Beagle já fornece alguns **widgets básicos** prontos que você pode utilizar para criar a interface dos componentes de sua aplicação server-driven. 

No entanto, a sua aplicação pode precisar de componentes mais especializados \(Custom Views\) e, para torná-los "visíveis"  ao Beagle, você precisa criar um **widget específico** para ele. Dessa forma, você pode criar quantos novos componentes desejar, desde que sempre **torne as Views do seu aplicativo "visíveis" para o Beagle.**

{{% alert color="danger" %}}
É **obrigatória** a adição da tag `@Transient` para **todos os atributos** presentes nas classes que representarão os widgets para que não sejam levados em conta na serialização e desserialização do componente.
{{% /alert %}}
