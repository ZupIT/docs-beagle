---
title: How to create a new action
weight: 222
description: "This section shows how to create a Custom Action that could be used in events and handled by the frontend application"
---

---

Although Beagle offers a lot of default Actions, there might be a situation that you need some extra features and fot that you can create a custom [**action**]({{< ref path="/api/actions" lang="en" >}}).
### Creating a frontend action

To create an action on the front end of your application, you may check the section [`Simple Custom Action`]({{< ref path="/android/customization/action/simple-action" lang="en">}})

### Creating an action in the backend

To create this action in the backend and use it in a screen built there, your code should look like this:

```kotlin
@RegisterAction
data class CustomAction(
val msg: String? = null
) : Action
```

Check the example below for an Action set in a Button click:

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
