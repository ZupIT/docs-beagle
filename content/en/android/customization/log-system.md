---
title: Log System
weight: 130
description: >-
  You will find here the BeagleLogger class description, customization and
  configuration details.
---

---

## Introduction

Beagle's log system allows you to investigate errors during the development and problems that may happen with the application's production. 

The log messages given by Android Beagle can be accessed in the:

* Android Studio's console
* Application's console 

The log system is divided in 3 categories: 

```kotlin 
interface BeagleLogger {

    fun warning(message: String)

    fun error(message: String)

    fun error(message: String, throwable: Throwable)

    fun info(message: String)
}
```

1. **Info:** related to the Network layer, information and communication's response with the server. 
2. **Warning:** informational messages of errors related to Beagle's component. 
3. **Error**: shows error messages related to  `parse`, `http` and other exceptions.

It is possible to **disable triggered log messages**. Beagle will not call the Log's API, even if it is default or customized. You will need to change the attribute `isLoggingEnabled` of`AppBeagleConfig` to`false:`


```kotlin
@BeagleComponent
class AppBeagleConfig : BeagleConfig {

    override val isLoggingEnabled: Boolean = false

}
```


## Customization

You will create a class that implements `BeagleLogger` interface. This protocol must be defined to ease the error management generated in the server-driven context of your application. You can implement the methods the best way for your application.

In order to let Beagle's environment open to modifications, the default API logs can be replaced by any other.

The customization is configured with the creation of an annotation class with `@BeagleComponent` that implements the `BeagleLogger` **Interface.** 

This interface needs logs method implementation, you can check how it works on the example below: 

```kotlin
import android.util.Log
import br.com.zup.beagle.android.annotation.BeagleComponent
import br.com.zup.beagle.android.logger.BeagleLogger

private const val BEAGLE_TAG = "BeagleSDK"

@BeagleComponent
class BeagleLoggerDefault : BeagleLogger {

    override fun warning(message: String) {
        Log.w(BEAGLE_TAG, message)
    }

    override fun error(message: String) {
        Log.e(BEAGLE_TAG, message)
    }

    override fun error(message: String, throwable: Throwable) {
        Log.e(BEAGLE_TAG, message, throwable)
    }

    override fun info(message: String) {
        Log.i(BEAGLE_TAG, message)
    }

}
```
