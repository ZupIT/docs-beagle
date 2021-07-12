---
title: Oninit Custom Widget
weight: 117
description: Creating a custom widget with onInit
---

---

Currently in Beagle, the [**Container**]({{< ref path="/api/components/layout/container" lang="en" >}}) and [**ListView**]({{< ref path="/api/components/layout/listview" lang="en" >}}) components have the behavior of executing a list of actions as soon as they are rendered. For this, they expose the `onInit` property, which receives the list of actions that will be triggered.

To create a custom widget with the behavior of triggering a list of actions as soon as it is rendered, Beagle provides an easy way to do it using the default delegate by implementing the `OnInitiableComponent` interface.

{{% alert color="info" %}}
For this step-by-step we will focus on linking `OnInitiableComponent` to the customizable widget, since its creation in detail has already been shown in the previous example.
{{% /alert %}}

### Step 1: Link `OnInitiableComponent`

With the customizable widget created, add the `OninitiableComponent` interface.

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

### Step 2: Implement `onInit` property

After adding the `OnInitiableComponent` interface to the widget, override the `onInit` property.

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

### Step 3: Delegate implementation

In addition to the `onInit` property, the `OnInitiableComponent` interface needs the implementation of the `handleOnInit` and `markToRerunOnInit` methods. To facilitate and maintain the component's default behavior, Beagle already provides an implementation and it is recommended to use it by delegating to `OnInitiableComponentImpl`.

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

### Step 4: Run `handleOnInit`

Finally, with all the configuration done in the widget, only the execution of the `handleOnInit` method is missing so that the list of `onInit` actions can be executed as soon as the view is rendered.

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

It's done! Your component now performs a list of actions whenever it is rendered!

{{% alert color="danger" %}}
It is mandatory to implement the `OnInitiableCompoent` interface for every custom widget that is inserted into the [ListView]({{< ref path="/api/components/layout/listview" lang="en" >}}) component.
{{% /alert %}}
