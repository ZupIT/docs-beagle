---
title: Widget Customizado com ServerDrivenComponent
weight: 135
description: Criando um widget customizado com ServerDrivenComponent
---

---

Para criar um widget customizado que utilize outros componentes do Beagle, basta implementar a interface `SingleChildComponent` quando houver somente um componente filho, ou `MultiChildComponent` para o widget que utilizar varios outros componentes.

{{% alert color="warning" %}}
Um componente deve implementar somente uma dessas interfaces, ou `SingleChildComponent`, ou `MultiChildComponent`, nunca as duas ao mesmo tempo.
{{% /alert %}}

## SingleChildComponent

Widgets customizados que renderizem um unico componente do Beagle devem implementar a interface `SingleChildComponent`.

### Passo 1: Vincular SingleChildComponent

Com o widget customizado criado, adicione a interface `SingleChildComponent`.

```kotlin
@RegisterWidget
class CustomChildWidget(
    val name: String,
    val phone: String,
    val age: Int
): WidgetView(), SingleChildComponent {
    override fun buildView(rootView: RootView): View {
        TODO("Not yet implemented")
    }
}
```

### Passo 2: Implementar a propriedade `child`

Após adicionar a interface `SingleChildComponent` ao widget, sobrescreva a propriedade `child`.

```kotlin
@RegisterWidget
class CustomChildWidget(
    val name: String,
    val phone: String,
    val age: Int,
    override val child: ServerDrivenComponent
): WidgetView(), SingleChildComponent {
    override fun buildView(rootView: RootView): View {
        TODO("Not yet implemented")
    }
}
```

### Passo 3: Utilize o componente

Para usar a view gerada a partir do componente `child`, basta considera-lo como um `ViewConvertable` e chamar o método `buildView`.

```kotlin
@RegisterWidget
class CustomChildWidget(
    val name: String,
    val phone: String,
    val age: Int,
    override val child: ServerDrivenComponent
): WidgetView(), SingleChildComponent {
    override fun buildView(rootView: RootView): View {
        val builtChild = (child as ViewConvertable).buildView(rootView)
    }
}
```

## MultiChildComponent

Widgets customizados que renderizem um ou mais componentes do Beagle devem implementar a interface `MultiChildComponent`.

### Passo 1: Vincular MultiChildComponent

Com o widget customizado criado, adicione a interface `MultiChildComponent`.

```kotlin
@RegisterWidget
class CustomChildWidget(
    val name: String,
    val phone: String,
    val age: Int
): WidgetView(), MultiChildComponent {
    override fun buildView(rootView: RootView): View {
        TODO("Not yet implemented")
    }
}
```

### Passo 2: Implementar a propriedade `children`

Após adicionar a interface `MultiChildComponent` ao widget, sobrescreva a propriedade `children`.

```kotlin
@RegisterWidget
class CustomChildWidget(
    val name: String,
    val phone: String,
    val age: Int,
    override val children: List<ServerDrivenComponent>
): WidgetView(), SingleChildComponent {
    override fun buildView(rootView: RootView): View {
        TODO("Not yet implemented")
    }
}
```

### Passo 3: Utilize o componente

Para usar a view gerada a partir do componente `children`, basta considerar cada um como `ViewConvertable` e chamar o método `buildView` de cada.

```kotlin
@RegisterWidget
class CustomChildWidget(
    val name: String,
    val phone: String,
    val age: Int,
    override val children: List<ServerDrivenComponent>
): WidgetView(), SingleChildComponent {
    override fun buildView(rootView: RootView): View {
        children.forEach { child ->
            val builtChild = (child as ViewConvertable).buildView(rootView)
        }
    }
}
```

Pronto! Seu componente agora utiliza outros componentes do Beagle!

{{% alert color="danger" %}}
É obrigatório a implementação de uma das duas interfaces para todo widget customizado que utilizar componentes do Beagle dentro do componente [ListView]({{< ref path="/api/components/layout/listview" lang="pt" >}}).
{{% /alert %}}
