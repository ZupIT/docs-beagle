---
title: Async Custom Action
weight: 106
description: In this section, you will find how to create a custom async action.
---

**Topics covered:**
 - How to create an Async Action

**Requirements:**
 - A project with Beagle configured;
 - Simple actions. For more information, check out [how to create simple actions]({{< ref path="/android/customization/action/simple-action" lang="en">}})

 ## How to create Assyn Actions

To create an action with asynchronous execution such as consuming an API or accessing a database, follow the steps:

**Step 1: Create an `action` as usual, just like the previous example;**
**Step 2: Implement the `AsyncAction` interface;**
**Step 3: Link the `AsyncAction` interface and delegate its implementation to the `AsyncActionImpl` class provided by Beagle.**

{{% alert color="info" %}}
It is recommended to use this class, as it implements the `onActionStarted` and `onActionFinished`, exposing its state in a reactive and standardized way in the framework.
{{% /alert %}}

```kotlin
@RegisterAction("customActionAndroid")
data class CustomActionAndroid(
    val value: String
) : Action, AsyncAction by AsyncActionImpl() {
    override fun execute(rootView: RootView) {
        // Do asynchronous work
    }
}
```

**Step 4: Now, the action is ready to execute asynchronously. It is MANDATORY to notify when the execution is finished through the `onActionFinished` method.**

```kotlin
@RegisterAction("customActionAndroid")
data class CustomActionAndroid(
    val value: String
) : Action, AsyncAction by AsyncActionImpl() {
    override fun execute(rootView: RootView) {
        // Do asynchronous work
        onActionFinished()
    }
}
```

It's done! Your action is now configured to run any job asynchronously!

{{% alert color="danger" %}}
It is mandatory to implement the `AsyncAction` interface for any custom action that is inserted within the [ListView]({{< ref path="/api/components/layout/listview" lang="en" >}}) component.
{{% /alert %}}
