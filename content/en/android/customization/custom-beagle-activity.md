---
title: Custom Beagle Activity
weight: 109
description: >-
  You will find here how to use BeagleActivity  to customize your server-driven
  activity.
---

---

## Introduction

`BeagleActivity` is a class that helps you manage where your server-driven components will be placed. Its structure works like an Android activity. 

In order to Beagle understand that you're working with an `BeagleAcitivity`'s class, it's necessary to use **`@RegisterController`** annotation and implement their respective methods.

## What is customizable?

When it comes to a Beagle Activity, there are some custom components:

1. Layout xml
2. Toolbar
3. Handling error
4. ProgressBar

Check out below how to customize a Beagle Activity:

```kotlin
@RegisterController
class SampleServerDrivenActivity : BeagleActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContentView(R.layout.activity_server_driven)
    }
    
    override fun getToolbar(): Toolbar {
        TODO("Not yet implemented")
    }

    override fun getServerDrivenContainerId(): Int {
        TODO("Not yet implemented")
    }

    override fun onServerDrivenContainerStateChanged(state: ServerDrivenState) {
        TODO("Not yet implemented")
    }
    
}
```

### 1. Layout xml

A xml layout file is where you host the screen component's structure, just like in an Android Activity. Even if you're using Beagle to send your screen structure through server-driven, it is still important to create the xml file to receive this content. 

See below an example of an activity xml layout: 

```kotlin
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/root_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <androidx.appcompat.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <FrameLayout
            android:id="@+id/server_driven_container"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />

        <ProgressBar
            android:id="@+id/progress_bar"
            android:layout_width="42dp"
            android:layout_height="42dp"
            android:layout_gravity="center"
            android:visibility="gone"/>

    </FrameLayout>

</LinearLayout>
```

For the server-driven content, the xml layout needs a frame layout to load this content.

In the `getServerDrivenContainerId()`method, you must return the frame layout id.

```kotlin
override fun getServerDrivenContainerId(): Int = R.id.server_driven_container
```

### 2. Toolbar

You can create a Toolbar and apply in your activity using the `getToolbar()`. In this method, you must return your Toolbar.

```kotlin
override fun getToolbar(): Toolbar = findViewById<Toolbar>(R.id.toolbar)
```

### 3. Handling error

You can watch the state and treat the errors from **`onServerDrivenContainerStateChanged()`**

You can observe a server-driven activity status and handle errors using the function [`onServerDrivenContainerStateChanged()`]({{< ref path="/resources/customization/beagle-for-android/loading-and-errors-treatment" lang="pt" >}}). This function has as parameter that indicates the status of a server-driven activity, and when any error occurs, the state `ServerDrivenState.Error` will be received at this function.

```kotlin
override fun onServerDrivenContainerStateChanged(state: ServerDrivenState) {
     if (state is ServerDrivenState.Error) {
          //Your error's treatment 
     }
}
```

### 4. ProgressBar

Like when you handled errors, you can check if the server-driven container status is `Started` or` Finished` and thus define the visibility of a *ProgressBar*. When the activity loading starts, the `onServerDrivenContainerStateChanged` function receives the` ServerDrivenState.Started` parameter (which represents the activity status). Likewise, when the loading ends without errors, the `ServerDrivenState.Finished` parameter is received. You can check below how we can use these two states to change the progress bar visibility.

```kotlin
override fun onServerDrivenContainerStateChanged(state: ServerDrivenState) {
    when (state) {
        is ServerDrivenState.Started -> {
            progressBar.visibility = View.VISIBLE
        }

        is ServerDrivenState.Finished -> {
            progressBar.visibility = View.GONE
        }
}
```

{{% alert color="info" %}}
You can register many activities as you wish, but **you must define a default BeagleActivity** using the annotation `@RegisterController(id = "ACTIVITY_IDENTIFIER")`.  
This `id` identify what activity the BFF will use. 
{{% /alert %}}
