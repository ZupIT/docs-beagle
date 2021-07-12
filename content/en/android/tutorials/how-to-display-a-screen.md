---
title: How to display a screen
weight: 224
description: >-
  In this section, you will find how to display a server-driven screen.
---

---

## Display a screen through a URL

To display a **`full server-driven`** screen, you need to use the method `this.newServerDrivenIntent<BeagleActivity>()`. Where `this` refers to the context of the screen.

This method requires the parameter `ScreenRequest`.

You must follow the example below:

{{% alert color="warning" %}}
Make sure to list your Beagle Activity as named in your AndroidManifestfile.

In this example, you only call it BeagleActivity, but this name is defined by you when creating your BeagleActivity. In the tutorial [**Creating a project from scratch**]({{< ref path="/get-started/creating-a-project-from-scratch/case-android" lang="en" >}}), it is called `AppBeagleActivity`.
{{% /alert %}}

```kotlin
val intent = this.newServerDrivenIntent<BeagleActivity>(ScreenRequest("/screen"))
startActivity(intent)
```

{{% alert color="info" %}}
To test the command above, the `ScreenRequest` component requires an URL that brings the JSON with all the server-driven screen's information that you want to display.

In this case, follow these steps:

- Use the screen example on the link below that brings a JSON for a simple `"\screen"` test. Just copy and paste it replacing the URL "/screen" .
  https://run.mocky.io/v3/73322be2-96bf-467b-b6dc-e3fff179852c

- See how the code ends up below.

```text
val intent = this.newServerDrivenIntent<BeagleActivity>(ScreenRequest("https://run.mocky.io/v3/73322be2-96bf-467b-b6dc-e3fff179852c"))
startActivity(intent)
```

{{% /alert %}}

### The Screen Request class.

The `ScreenRequest` is an internal Beagle class used to request which screen you want to display. You will only list the **`URL`** attribute for the page you want to load from the BFF. However, this element has other attributes, which can be used in the transition and between screens. 

To learn more about this class, check out on [**Screen Request**]({{< ref path="/api/screen-request" lang="en" >}})

## Display a screen through a JSON

To render any Beagle component in Android, it's necessary to inform a `viewGroup` where the view corresponding to the component will be rendered, such as a `FrameLayout` within an `Activity`, `Fragment` or `DialogFragment`. See the example below:

```markup
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/frame_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"/>
```

Now, call the `loadView()` method from the `frame_layout` created in the `xml` and pass the `Activity` and the `JSON` as parameters.

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        frame_layout.loadView(
            activity = this,
            screenJson = "JSON HERE"
        )
    }
}
```

{{% alert color="info" %}}
The `loadView()` method can also receive as a parameter a `Fragment` in place of `Activity` and two other optional ones, following the example below:

```kotlin
loadView(
  fragment = yourFragment, 
  screenJson = "JSON HERE",
  screenId = "your identifier",
  shouldResetContext = yourBoolean,
)
```

### Optional parameters
`screenId` : represents an screen identifier to create the analytics when the screen is created. Default value is empty.
`shouldResetContext` : when true, this clear at the time of calling the `loadView` function all de context data linked to the lifecycle owner. Default value is false.

{{% /alert %}}
