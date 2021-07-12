---
title: Como criar novas actions
weight: 222
description: "Nesta seção, você confere como criar Actions Customizadas para serem usadas em eventos mas sendo tratadas pela aplicação frontend"
---

---

### Criando uma ação do frontend

No Beagle, é possível criar uma [**ação**]({{< ref path="/api/actions" lang="pt" >}}) customizada a partir de uma `CustomAction`.

Para criar uma ação no front end da sua aplicação, veja a seção [Ações Customizadas Simples]({{< ref path="/android/customization/action/simple-action" lang="pt">}})

### Criando uma ação no backend

Para criar essa ação no backend e utiliza-la em uma tela construida no backend, o seu código deve estar assim:

```kotlin
@RegisterAction
data class CustomAction(
val msg: String? = null
) : Action
```

Veja abaixo um exemplo de uma Ação em um componente do tipo Button:

{{< tabs id="T93" >}}
{{% tab name="JSON" %}}

```kotlin
{
  "_beagleComponent_": "beagle:button",
  "text": "Beagle Button",
  "onPress": [
    {
      "_beagleAction_": "custom:customAction",
      "msg": "Eu sou um Toast."
    }
  ]
}
```

{{% /tab %}}

{{% tab name="Kotlin DSL" %}}

```kotlin
Button(
   text = "Beagle Button",
   onPress = listOf(CustomAction("Eu sou um Toast"))
)
```

{{% /tab %}}
{{< /tabs >}}
