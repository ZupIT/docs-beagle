---
title: Design System
weight: 124
description: You will find here Design System class description and methods details.
---

---

## Introduction

You can register **styles** as fonts, color, font size and customized components with design system, which was added on Beagle 1.0.0.

It is the **design system** that stores the style components registry created in the frontend and then your Android application will know which style component it must be applied in each server-driven screen. It is through it  that views elements are used to create your screen.

To define a design system in your application, you have to create a class that **extends an Open class** `DesignSystem`. It will overwrite functions that are used to apply sytles to the components, inside each function you can declare several styles. 

Every style displayed with Beagle must be referenced in a way each one of them has a name to be identified.

### Example

The image method is registered in a series of images that will be used in the page construction coming from the backend. The operator `when` is used to designate the styles, the first listed value is a string that identifies the Beagle's style component\(very similiar to key/value\) and the component's style was listed with the `Arrow Function ->`.

This function's operator **else** will contain a default style, like the code block below:


```kotlin
// This class is used by the RenderEngine in order to define the styles configured at the application.
// It must extend DesignSystem.
// These are the styles the AppDesignSystem implements: image, textStyle, inputTextStyle, buttonStyle, toolbarStyle and tabViewStyle

@BeagleComponent
class AppDesignSystem : DesignSystem() {

    override fun image(id: String): Int? {
        return when (name) {
            "delete" -> android.R.drawable.ic_delete
            "logo" -> android.R.drawable.ic_logo
            "banner" -> android.R.drawable.ic_banner
            else -> android.R.drawable.ic_menu_help
        }
    }
    
    override fun textStyle(id: String): Int? {
        return when (name) {
            "TextBlackBold" -> R.style.TextBlackBold
            else -> R.style.TextBlackBold
        }
    }
    
    override fun inputTextStyle(id: String): Int? {
        return R.style.TextBlack
    }

    override fun buttonStyle(id: String): Int? = R.style.ButtonBlack
    
    override fun toolbarStyle(id: String): Int? = R.style.Toolbar
    
    override fun tabViewStyle(id: String): Int? {
        return R.style.CustomTab
    }
}
```


{{% alert color="info" %}}
When a class extends the `Open Class DesignSystem` it is able to manage styles that will be applied to the server-driven components. It is necessary to implement a class to register the styles on Beagle, only this way it will be able to see the styles and apply them.
{{% /alert %}}

As an Android pattern, the styles can be defined in the `style` file and referenced inside a class that understands Design System.

```markup
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <style name="TextBlackBold" parent="TextAppearance.AppCompat">
        <item name="android:textColor">#000000</item>
        <item name="android:textSize">20sp</item>
        <item name="android:textStyle">bold</item>
    </style>

    <style name="TextBlack" parent="TextAppearance.AppCompat">
        <item name="android:textColor">#000000</item>
        <item name="android:textSize">20sp</item>
    </style>

    <style name="ButtonBlack" parent="Widget.AppCompat.Button">
        <item name="android:textColor">#000000</item>
        <item name="android:textSize">18sp</item>
        <item name="android:textAllCaps">false</item>
    </style>

    <style name="Toolbar" parent="Widget.AppCompat.Toolbar">
        <item name="android:background">@color/colorPrimary</item>
        <item name="navigationIcon">?attr/homeAsUpIndicator</item>
    </style>

</resources>
```
