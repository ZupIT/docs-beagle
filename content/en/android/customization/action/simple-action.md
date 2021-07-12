---
title: Simple Custom Action
weight: 104
description: Creating and executing a custom action
---

**Topics Covered:**
 - What is an Action
 - How to create an Action 
 - How to register an Action
 - How to use an Action

**Requirements:**
 - A project with Beagle configured

 ## What is?

 On Beagle, an action deals with behaviours \(functions\), that are performed in your application when an event is triggered. These actions can be a Beagle's default or customized by you. 

## How to create an action

To create your custom action, follow the next steps:

Step 1. Create an annotated class with `@RegisterAction` that implements an `Action` interface;
Step 2. Put the action name by annotation parameter to avoid problems with Proguard;
Step 3. After that, the interface will request to implement the `execute` method;
Step 4. Now, declare the `action` result.

The `value` attribute is a parameter example that can be declared in this class constructor. 
The following example shows an action with **Toast** receiving a text with a parameter: 


```kotlin
@RegisterAction("customActionAndroid")
data class CustomActionAndroid(
    val value: String
) : Action {
    override fun execute(rootView: RootView) {
        Toast.makeText(
            rootView.getContext(), 
            value, 
            Toast.LENGTH_LONG).show()
    }
}    
```

## How to register your action

There are two ways to register your action, check them below: 

 - Using Annotation Processor:

    For this, annotate your action class with `@RegisterAction("className")` where className is your class name.

 - Without the Annotation Processor

## How to use your action

See below, an action example in a button type component: 

```kotlin
Button(
   text = text,
   styleId = styleId,
   onPress = listOf(CustomActionAndroid("I'm a customized action"))
)
```
