---
title: Navigation Animation
weight: 132
description: >-
  You will learn in this section about how it is possible to customize Beagle's
  navigation screen
---

---

## Introduction

Beagle has a [**screen navigation**]({{< ref path="/resources/screen-navigation" lang="en" >}}) tool that allows you to configure actions like navigate to a new screen or close one. 

Besides these configuration, you can also customize the navigation of your application. 

There are two customization options:

1. According to Android's default animation.
2. Fragments transition provided by Beagle, that it can be customized according to your preference and design system. They are: 

* [**PushView**]({{< ref path="/api/actions/navigate/pushview" lang="en" >}})
* [**PopView**]({{< ref path="/api/actions/navigate/popview" lang="en" >}})
* [**PopToView**]({{< ref path="/api/actions/navigate/poptoview" lang="en" >}})

## Protocol and customized 

**Beagle's default protocol** for customization is to use a native Android tool, to make fragment transition to customize your animation.

{{% alert color="info" %}}
For more information on how this process works, [**check out the Android's section**  **setCustomAnimations**.](https://developer.android.com/reference/android/app/FragmentTransaction#setCustomAnimations%28int,%20int,%20int,%20int%29)
{{% /alert %}}

The other way to customize an animation is the transition through the [**BeagleActivity**]({{< ref path="/get-started/creating-a-project-from-scratch/" lang="en" >}}) class implementation, which Beagle uses the _**getFragmentTransitionAnimation\(\)**_ method.

In case this method it isn't implemented and customized, the transitions will follow Beagle's default animation. The code below shows the [**BeagleActivity**]({{< ref path="/get-started/creating-a-project-from-scratch/" lang="en" >}}) method, that can be overwritten this way:

```kotlin
open fun getFragmentTransitionAnimation() = FragmentTransitionAnimation(
    enter =  R.anim.slide_from_right,
    exit = R.anim.none_animation,
    popEnter = R.anim.none_animation,
    popExit = R.anim.slide_to_right
)
```
