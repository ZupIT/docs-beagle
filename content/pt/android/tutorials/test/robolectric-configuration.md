---
title: Configuração do Robolectric
weight: 239
description: Configuração Robolectric para testes unitários de Android
---

---

## O que é?

O Roboletric é um **framework para testes unitários** no Android. Uma de suas funções é simular informações sobre o contexto do Android durante a realização desses testes.

Antes de utilizá-lo, é preciso configurar uma dependência do Yoga Layout chamada de **SoLoader** que, se não estiver habilitado para **modo TESTE**, vai ocorrer um erro de **nullPointerException**, uma vez que a view não existirá naquele contexto.

{{% alert color="info" %}}
Vale lembrar que o Beagle utiliza **Yoga Layout** para [**posicionar os componentes na tela**]({{< ref path="/resources/components-positioning/" lang="pt" >}})
{{% /alert %}}

Sendo assim, você precisa configurar o nosso SDK para ativar o "TesteMode" no SoLoader quando iniciar um teste e desativá-lo assim que terminar. Para tal, basta utilizar as funções conforme indicado a seguir.

## Como configurar?

Para configurar o Beagle para os testes do **Roboeletric**, adicione o código abaixo no seu caso de teste:

```kotlin
@BeforeClass
fun setup() {
    BeagleSdk.setInTestMode()
}

@AfterClass
fun teardown() {
    BeagleSdk.deinitForTest()
}
```
