---
title: Using Widget Id
weight: 241
description: How to set and get id on a widget
---

---

To test your application, you may need a Widget ID. In order to do that, there is some methods on Beagle to **set** and **get** this Widget IDs.

On this section, you'll see two specific ones:

1. **Set Id**
2. **Get Id**

## Set Id

The method Set Id is used when you need to set an Id on a [**widget**]({{< ref path="/api/widget" lang="en" >}}) provided by the [**BFF**]({{< ref path="/key-concepts#backend-for-frontend" lang="en" >}}). To do so, you just have to call this method on the widget you want to set the Id during the moment you're creating your widget.

See how this works on the code below:

```kotlin
Text(text = "Text with id").setId("myTextId")
```

## Get Id

On Android, to get an Widget Id you just need to call the `String.toAndroidId()` and this method will return the Id to use where you need.

See how it works on the like below:

```kotlin
"myTextId".toAndroidId()
```

