---
title: Configuração do Proguard
weight: 117
description: >-
  Nesta seção, você encontra a descrição da configuração do ProGuard para o
  Beagle.
---

---

## Introdução

O Beagle contém regras mínimas necessárias para que seus componentes funcionem sem a necessidade da sua aplicação adicionar alguma regra no ProGuard / R8. 

Para aplicar essas regras, você precisa preencher as anotações do `RegisterWidget` e `RegisterAction` com seus respectivos nomes. Caso haja algum objeto componente dentro do seu componente, você deve anotá-lo com `@BeagleJson`. 

## Exemplo

Para garantir que seu componente funcione normalmente com o ProGuard / R8 ativo, você deve anotar as classes utilizadas com a anotação `@BeagleJson:`

```kotlin


@BeagleJson
enum class MyType {
    PASSWORD, TEXT;
}

@BeagleJson
data class MyText(val textOne: String, val textTwo: String)

@RegisterWidget("myText")
data class Text(
    val text: Bind<String>,
    val type: MyType,
    val myText: MyText,
) : WidgetView() {

    override fun buildView(rootView: RootView): TextView
      = TextView(rootView.getContext())

}

@RegisterAction("CustomAndroidAction")
data class CustomAndroidAction(
    val value: String,
    val intValue: Int
) : Action {
    override fun execute(rootView: RootView, origin: View) { }
}

```

{{% alert color="info" %}}
Aqui, você também habilita o `minifyEnable` e `shrinkResources` no `buildType` declarando-os como _**true**_ para testar a ofuscação do ProGuard.
{{% /alert %}}

Você deve ativar o ProGuard  na sua IDE. Para testá-lo, você pode usar o Android Studio adicionando a configuração listada abaixo:

```text
buildTypes {
    release {
        minifyEnabled true
        shrinkResources true
        proguardFiles getDefaultProguardFile(
            'proguard-android-optimize.txt'),
            'proguard-rules.pro'
    }
}
```
