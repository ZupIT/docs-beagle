---
title: Widget Customizado com Oninit
weight: 133
description: Criando um widget customizado com onInit
---

---

Atualmente no Beagle, os componentes [**Container**]({{< ref path="/api/components/layout/container" lang="pt" >}}) e [**ListView**]({{< ref path="/api/components/layout/listview" lang="pt" >}}) possuem o comportamento de executar uma lista de ações assim que são renderizados. Para isso, eles expõe a propriedade `onInit`, a qual recebe a lista de ações que serão disparadas.

Para criar um widget customizado com o comportamento de disparar uma lista de ações assim que for renderizado, o Beagle fornece uma forma fácil de fazer utilizando o pattern delegate através da implementação da interface `OnInitiableComponent`.

{{% alert color="info" %}}
Para esse passo-a-passo vamos focar na vinculação do `OnInitiableComponent` ao widget customizável, já que a criação dele em detalhes já foi mostrada no exemplo anterior.
{{% /alert %}}

### Passo 1: Vincular `OnInitiableComponent`

Com o widget customizável criado, adicione a interface `OninitiableComponent`.

```kotlin
@RegisterWidget
class CustomInitiableWidget(
    val name: String,
    val phone: String,
    val age: Int
): WidgetView(), OnInitiableComponent {
    override fun buildView(rootView: RootView): View {
        TODO("Not yet implemented")
    }
}
```

### Passo 2: Implementar propriedade `onInit`

Após adicionar a interface `OnInitiableComponent` ao widget, sobrescreva a propriedade `onInit`.

```kotlin
@RegisterWidget
class CustomInitiableWidget(
    val name: String,
    val phone: String,
    val age: Int,
    override val onInit: List<Action>?
): WidgetView(), OnInitiableComponent {
    override fun buildView(rootView: RootView): View {
        TODO("Not yet implemented")
    }
}
```

### Passo 3: Delegar implementação

Além da propriedade onInit, a interface `OnInitiableComponent` precisa da implementação dos métodos `handleOnInit` e `markToRerunOnInit`. Para facilitar e manter o padrão de comportamento do componente, o Beagle já fornece uma implementação pronta e recomendamos o seu uso, bastando somente delegar `OnInitiableComponentImpl`.

```kotlin
@RegisterWidget
class CustomInitiableWidget(
    val name: String,
    val phone: String,
    val age: Int,
    override val onInit: List<Action>?
): WidgetView(), OnInitiableComponent by OnInitiableComponentImpl(onInit) {
    override fun buildView(rootView: RootView): View {
        TODO("Not yet implemented")
    }
}
```

### Passo 4: Executar `handleOnInit`

Por fim, com toda a configuração feita no widget, falta somente a execução do método `handleOnInit` para que a lista de ações `onInit` sejam executadas assim que a view for renderizada.

```kotlin
@RegisterWidget
class CustomInitiableWidget(
    val name: String,
    val phone: String,
    val age: Int,
    override val onInit: List<Action>?
): WidgetView(), OnInitiableComponent by OnInitiableComponentImpl(onInit) {
    override fun buildView(rootView: RootView): View {
        handleOnInit(rootView, view)
    }
}
```

Pronto! Seu componente agora executa uma lista de ações sempre que for renderizado!

{{% alert color="danger" %}}
É obrigatório a implementação da interface `OnInitiableCompoent` para todo widget customizado que for inserido dentro do componente [ListView]({{< ref path="/api/components/layout/listview" lang="pt" >}}).
{{% /alert %}}
