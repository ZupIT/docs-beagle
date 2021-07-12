---
title: Beagle Scaffold
weight: 16
description: 'Here you will find the **Beagle-Scaffold** library to help you start a project using Beagle in Android.
This lib will hold almost all necessary configuration to add Beagle into an Android project.'
---

---


![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-scaffold?color=green&label=Beagle-Scaffold)
![**Maven Central**](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)

### Requirements
<hr>

Before you start to configure Beagle for your Android system, it’s important to check out if you have installed all the current versions of the following programs: ‌

* JDK 8+ language
* Android API level: at least 19 or higher
* Kotlin 1.3+

### Getting started
<hr>

This tutorial will configure Beagle from the Start.

#### Step 1: Add a configuration on the Gradle file in your project:
- `Add mavenCentral()` into the `All projects` block on your  Project Gradle file(build.gradle)<br>

```kotlin
allprojects {
    repositories {
        google()
        jcenter()
        mavenCentral()
    }
}
```
#### Step 2: Add kotlin kapt plugin and 3 dependencies on your application dependency manager (gradle.gradle)<br>

 ![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-scaffold?color=green&label=Beagle-Scaffold)
 ![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)                                                                   

* Add the packagingOptions code block to the build.gradle file, as in the example below.

* _The version numbers listed below may have been updated. Please check the actual versions on the tags above:_
```kotlin
plugins {
    id 'com.android.application'
    id 'kotlin-android'
    id 'kotlin-android-extensions'
    id 'kotlin-kapt'
}

android {
    kotlinOptions {
        jvmTarget = JavaVersion.VERSION_1_8
    }

}

dependencies {
    implementation "br.com.zup.beagle:android:1.7.0"
    kapt "br.com.zup.beagle:android-processor:1.7.0"
    implementation "br.com.zup.beagle:beagle-scaffold:1.7.0"
}
```

#### Step 3: Create an AppBeagleConfigFile
* This file will get all its configuration attributes from the beagle-scaffold lib.<br>
if you need a more detailed information check our documentation

```
@BeagleComponent
class AppBeagleConfig: BeagleConfigScaffold()
```
#### Step 4: Build your project: 
When building this application the config above will setup Beagle in your project.<br>
* Click on the **Build> Make Project** tab



#### Step 5: Create an AppAplication file as the example below:
```
class AppApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        BeagleScaffold(BeagleSetup()).init(this)
    }
}
```
#### Step 6: Updating the Android Manifest

In this last step you will add two configurations to the android manifest: The internet permission and your `application's name`

* Add the code line below to enable the internet permission:

```markup
<uses-permission android: name = "android.permission.INTERNET" />
```

* Add your application's name into the android manifest:

```markup
<application
        android: name = ". AppApplication"

        ...
```

* Your code should look like the example below:

```markup
<? xml version = "1.0" encoding = "utf-8"?>
<manifest xmlns: android = "http://schemas.android.com/apk/res/android"
    package = "com.example.beagleexamples">

    <uses-permission android: name = "android.permission.INTERNET" />

    <application
        android: name = ". AppApplication"

        ...
```


#### Step 7: Test your application<br>
To test it you just need to call a screen from our BFF sample.<br>
To do that you need to start an activity using the BeagleIntent <br>
from the Beagle-Scaffold lib exactly as shown below:
```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        //Server-driven Screen
        startActivity(BeagleIntent(this).toSample())
    }
}
```
A sample screen with all Beagle components will show on your emulator screen

<hr>

##### For more on Beagle: Please check our [Beagle](https://github.com/ZupIT/beagle) repository.
##### For more on Beagle-Defaults: Please check our [Beagle-Default]({{< ref path="/get-started/using-beagle-helpers/android/beagle-defaults" lang="en" >}}) documentation.
