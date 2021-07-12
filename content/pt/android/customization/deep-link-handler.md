---
title: Deep Link Handler
weight: 119
description: >-
  Nesta seção, você encontra a descrição da classe DeepLinkHandler e detalhes
  dos métodos que ele implementa.
---

---

## Introdução

O **`DeepLinkHandler`** é uma **interface** que define como configurar a navegação entre uma
**tela Server-Driven** e uma **tela nativa.**

## Exemplo

### Criando um DeepLinkHandler

Para realizar essa configuração, você deve criar uma classe que implemente a interface `DeepLinkHandler` e que faça a anotação com **`@BeagleComponent`**, conforme listado abaixo:

```kotlin
@BeagleComponent
class AppDeepLinkHandler : DeepLinkHandler {
    override fun getDeepLinkIntent(
        rootView: RootView,
        path: String,
        data: Map<String, String>?,
        shouldResetApplication: Boolean
    ) = Intent(path)
}
```

No método _getDeepLinkIntent_, você consegue configurar a navegação de telas de fluxos server driven ui para suas telas nativas, fazendo com que e o beagle reconheça a sua regra.

| **Atributo**           | **Tipo**             |                                                          **Definição**                                                           |
| :--------------------- | :------------------- | :------------------------------------------------------------------------------------------------------------------------------: |
| rootView               | RootView             |                                        RootView tem a referência da activity ou fragment                                         |
| path                   | String               |                  Parâmetro de rota que pode ser definido via Navigate.OpenNativeRoute(route: "navigate.myview")                  |
| data                   | Map<String, String>? | Mapa de parâmetros que pode ser definido via OpenNativeRoute(route = "navigate.myview" , data = mapOf("param1" to "paramValue")) |
| shouldResetApplication | Boolean              |           Abre uma tela com a rota informada a partir de um novo fluxo e limpa a pilha de telas de todo o aplicativo.            |

Feito isso, é necessário modificar o arquivo de manifesto do Android:

1. **Passo 1:** Você deve adicionar um `intent-filter` na `Activity` para a qual deseja navegar.
2. **Passo 2:** Você deve adicionar uma `Action` que identificará essa `Activity`. O nome que usamos aqui foi `"navigate.myview"`.
3. **Passo 3:** Adicione uma tag `category` e a nomeie como `"android.intent.category.DEFAULT"`, assim como no exemplo abaixo.

```markup
//AndroidManifest
<activity android:name=".activities.DeepLinkActivity">
    <intent-filter>
        <action android:name="navigate.myview"/>
        <category android:name="android.intent.category.DEFAULT"/>

    </intent-filter>
</activity>
```

Agora você só precisa chamar a [**Action Navigate**]({{< ref path="/api/actions/navigate/" lang="pt" >}}) com o método [**OpenNativeRoute**]({{< ref path="/api/actions/navigate/opennativeroute" lang="pt" >}}) como o exemplo abaixo:

```kotlin
//Widget used to navigate to a native screen
Button(
    text = "Click to navigate!",
    onPress = listOf(
        Navigate.OpenNativeRoute("navigate.myview")
    )
)
```
