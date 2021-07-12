---
title: ServerDrivenComponent Custom Widget
weight: 119
description: Creating a custom widget with ServerDrivenComponent
---

---

To create a custom widget that uses other Beagle components, just implement the `SingleChildComponent` interface when there is only one child component, or `MultiChildComponent` for the widget that uses several other components.

{{% alert color="warning" %}}
A component must implement only one of these interfaces, or `SingleChildComponent`, or `MultiChildComponent`, never both at the same time.
{{% /alert %}}

## SingleChildComponent

Custom widgets that render a single Beagle component must implement the `SingleChildComponent` interface.

### Step 1: Link `SingleChildComponent`

With the custom widget created, add the `SingleChildComponent` interface.

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

### Step 2: Implement the `child` property

After adding the `SingleChildComponent` interface to the widget, override the `child` property.

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

### Step 3: Use the component

To use the view generated from the `child` component, just consider it as a `ViewConvertable` and call the `buildView` method.

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

Custom widgets that render one or more Beagle components must implement the `MultiChildComponent` interface.

### Step 1: Link `MultiChildComponent`

With the custom widget created, add the `MultiChildComponent` interface.

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

### Step 2: Implement the `children` property

After adding the `MultiChildComponent` interface to the widget, override the `children` property.

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

### Step 3: Use the component

To use the view generated from the `children` component, just consider each one as `ViewConvertable` and call their `buildView` method.

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

It's done! Your component now uses other Beagle components!

{{% alert color="danger" %}}
It is mandatory to implement one of the two interfaces for any custom widget that uses Beagle components within the [ListView]({{< ref path="/api/components/layout/listview" lang="en" >}}) component.
{{% /alert %}}
