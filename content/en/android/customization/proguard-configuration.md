---
title: Proguard configuration
weight: 136
description: 'You will find here, ProGuard description and configuration for Beagle.'
---

---

## Introduction

Beagle contains the ProGuard / R8 rules necessary for its components to work correctly.

To make sure these rules work, you must fill the `RegisterWidget` and ` RegisterAction` annotations with their respective names. In case you have an object inside your component, you will need to annotate it with `@BeagleJson`

## Example

You have to annotate the classes used with the `@BeagleJson`  annotation  to make sure that your component works normally with ProGuard / R8 active.


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
You also have to enable `minifyEnable` and `shrinkResources` on `buildType` declaring them as true to test the ProGuard obfuscation.
{{% /alert %}}

You must activate ProGuard in your IDE. To test it, you can use Android Studio by adding the listed configuration below:

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
