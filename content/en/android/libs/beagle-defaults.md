---
title: Beagle Defaults
weight: 17
description: 'Here you will find the **Beagle-Defaults** library to help you start a project using Beagle in Android.
This lib will hold just a few classes necessary 
to configure and add Beagle into an Android project.'
---

---

![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-defaults?color=green&label=Beagle-Defaults)
![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)

### Requirements
<hr>

Before you start to configure Beagle for your Android system, it’s important to check out if you have installed all the current versions of the following programs: ‌

* JDK 8+ language
* Android API level: at least 19 or higher
* Kotlin 1.3+

### Getting started
<hr>

This tutorial will configure Beagle from the Start.

Step 1: Add a configuration on the Gradle file in your project:
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
Step 2: Add kotlin kapt plugin and 3 dependencies on your application dependency manager (gradle.gradle)<br>

 ![**Maven Central**](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-defaults?color=green&label=Beagle-Defaults)
 ![**Maven Central**](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)                                                                   

* _The version numbers listed below may have been updated. Please check the actual versions on the tags above:_
```kotlin
apply plugin: 'kotlin-kapt'

android {
    kotlinOptions {
        jvmTarget = JavaVersion.VERSION_1_8
    }
}

dependencies {
    implementation "br.com.zup.beagle:android:1.5.1"
    kapt "br.com.zup.beagle:android-processor:1.5.1"
    implementation "br.com.zup.beagle:beagle-defaults:0.0.4"
}
```

Step 3: Create 4 configurations files for Beagle.
* These files will set a few configurations and classes used by Beagle.<br>
    * if you need a more detailed information check our documentation

a) Create an AppBeagleConfig file:
```
@BeagleComponent
class AppBeagleConfig: BeagleConfig {
    override val environment: Environment = Environment.DEBUG
    override val baseUrl: String = "https://adopt-beagle.continuousplatform.com/scaffold"
    override val isLoggingEnabled: Boolean = true
    override val cache: Cache = Cache(
        enabled = false,
        maxAge = 300,
        size = 15
    )
}
```
b) Create an HttpApp file as listed below. This class extends from an ´HttpClientDefault()´ class at the Beagle-Default lib:
```
@BeagleComponent
class HttpApp: HttpClientDefault()
```

c) Create a CacheApp file as listed below. This class extends from an ´StoreHandlerDefault()´ class at the Beagle-Default lib:
```
@BeagleComponent
class CacheApp : StoreHandlerDefault(AppApplication.APPLICATION!!)
```

d) Create a LoggerApp file as listed below. This class extends from a ´BeagleLoggerDefault()´ class at the Beagle-Default lib:
```
@BeagleComponent
class LoggerApp: BeagleLoggerDefault()
```

Step 4: Build your project: When building this application the config above will setup Beagle in your project.<br>
Step 5: Create an AppAplication file as the example below:
```
class AppApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        APPLICATION = this

        BeagleSetup().init(this)
    }

    companion object {
        var APPLICATION: Application? = null
    }
}
```
Step 6: Test your application<br>
To test it you just need to call a screen from our BFF sample.<br>
To do that you need to start an activity using the configuration below:
```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
       
        val intent = this.newServerDrivenIntent<ServerDrivenActivity>(ScreenRequest("/components"))
        startActivity(intent)
    }
}
```
A sample screen with all Beagle components will show on your emulator screen

<hr>

##### For more on Beagle: Please check our [Beagle](https://github.com/ZupIT/beagle) repository.
##### For more on Beagle-Scaffold: Please check our [Beagle-Scaffold]({{< ref path="/get-started/using-beagle-helpers/android/beagle-scaffold" lang="en" >}}) documentation.
