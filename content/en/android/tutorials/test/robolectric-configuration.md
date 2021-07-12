---
title: Robolectric Configuration
weight: 239
description: Robolectric configuration for Android unit tests
---

---

## What is it?

Roboletric is a **framework for unit testing** on Android. One of its functions is to simulate information about Android's context during the tests performed.

It's necessary to configure a dependency of Yoga Layout called **SoLoader**, that, if it's not enabled as **TEST mode**, a **nullPointerException** error will occur, since the view will not exist in that context.
 

{{% alert color="info" %}}
It's worth to remember that Beagle uses **Yoga Layout** to [**position the screen components**]({{< ref path="/resources/components-positioning/" lang="en" >}}).

{{% /alert %}}

Therefore, the SDK is configured to activate "TesteMode" in SoLoader when starting a test and deactivating it when finished. To do so, just use the functions as indicated below.

## How to configure?

To configure Beagle in for **robolectric** tests, add the code below in your test case:

```kotlin
@BeforeClass
fun setup() {
    BeagleSdk.setInTestMode()
}

@AfterClass
fun teardown() {
    BeagleSdk.deinitForTest()
}
```
