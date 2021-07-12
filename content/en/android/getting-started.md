---
title: Getting Started
weight: 4
description: In this section, you'll learn how to create an Android project with Beagle
---
**Topics covered**
- Create an Android project from scratch
- Add Beagle in your project (can be in an existing project)
- Configure Beagle
- Possible customizations

**Requirements:**
 - Android Studio
 - minSdkVersion: 19+
 - JDK 8+ language
 - Kotlin 1.3+

## Starting an Android project

For this example, we'll use Android Studio IDE. If you haven't installed it yet, just access the documentation [**official Android website** ](https://developer.android.com/studio?hl=us-en) and follow the instructions.   
  
After you have installed Android Studio, follow the steps below:

**Step 1: Open Android Studio and click on Start a new Android Studio project**

{{< figure src="/shared/newandroidproject.png">}}

**Step 2: Choose the Empty Activity option and click on next**. 

{{< figure src="/shared/androidprojecttemplate.png">}}

**Step 3️: On this page, there is some important information:**

* Inform your project's name. On this example, we'll call it `BeagleApp`.
* Choose which language you'll use. For Beagle, you should go with `Kotlin`.
* Choose **SDK minimum 19 or above**, because a lower SDK won't be compatible. 
* Define a **package** and a **Save location** according to your preference. 
* Click on **Next**.

{{< figure src="/shared/configureandroidproject.png">}}


**Step 4️: After the previous configurations, Android will take some time to build the project since it will synchronize all dependencies to initialize this project.**

Once the initialization is done, you will see this page: 

{{< figure src="/shared/mainactivity.png">}}

##  Add Beagle on your project 

**Step 1: Configure the dependencies**

This configuration is perfomed on `build.gradle`

You must add `mavenCentral()` in the *allprojects repository's dependencies* to set the resource for Beagle. To do so, use the following configuration to **download Beagle's library.**

```kotlin
allprojects {
    repositories {
        google()
        mavenCentral()
    }
}
```

{{% alert color="warning" %}}
The above configuration must be added to `allprojects{}`
{{% /alert %}}

**Step 2: Include plugins**

Now you need to include the `kapt` and `Beagle's` plugin on your dependency manager, check out the example below:

The current release version of Beagle is: [![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/android)](https://mvnrepository.com/artifact/br.com.zup.beagle/android)


```kotlin
plugins {
	id 'kotlin-kapt'
}

android {
    kotlinOptions {
        jvmTarget = JavaVersion.VERSION_1_8
    }
}

ext.beagle_version = "${beagle_version}"

dependencies {
    implementation "br.com.zup.beagle:android:$beagle_version"
    kapt "br.com.zup.beagle:android-processor:$beagle_version"
}
```


Insert Beagle's release version to replace ${beagle.version}`, this is necessary to put Beagle's version highlighted in a blue badge above, without the **v character**.

For example: undefined-`ext.beagle.version = "1.8.0"`

{{% alert color="warning" %}}
Remember to always check if you're using the latest version of Beagle. To see this information, you just have to pass your cursor above the version number. After that, sync your project.
{{% /alert %}}

{{% alert color="warning" %}}
Remember to use the same Beagle's version used on your BFF to avoid problems.
{{% /alert %}}

## Configure Beagle

After Beagle's installation, now you have to make our tool's usage configuration, follow the steps below:

**Step 1: Create a BeagleConfig class**

After the update, you have to create a `BeagleConfig` class and configure your attributes,  see the example below:

```kotlin
@BeagleComponent
class AppBeagleConfig : BeagleConfig {
    override val environment: Environment get() = Environment.DEBUG
    override val baseUrl: String get() = "https://myapp.server.com"
    override val isLoggingEnabled: Boolean = true
    override val cache: Cache = Cache(
        enabled = false,
        maxAge = 300,
        size = 15
    )
}
```

| **Attribute** | **Type** | **Definition** |
| :----------- | :------ | :------ |
| environment | Environment | Attribute responsible for informing Beagle about the current build status of the application. |
| baseUrl | String | Informs the base URL used in Beagle in the application. If you are using the emulator, [**check here to define this attribute**](https://developer.android.com/studio/run/emulator-networking.html#networkaddresses)  |
| isLoggingEnabled | Boolean | Attribute that enables or disables all logs that Beagle generates.|
| cache	| Cache	| Object responsible for managing the cache of Beagle requests. |

{{% alert color="warning" %}}
Make sure to note your class configuration with `BeagleComponent`, because Beagle expect them to have empty constructors.
{{% /alert %}}

**Step 2: Create a BeagleSetup**

After you have created the class shown in the previous step and annotated with @BeagleComponent, just build your project (access the menu Build > Make Project) and Beagle will automatically create a BeagleSetup class, see the image below:

{{< figure src="/shared/beaglesetup.png">}}

**Step 3: Create the Application class**

At this moment, you should create a `Kotlin class` that extends the `Application class`. For this example, the name will be `AppApplication`.

This class should be named as `BeagleSetup().init(this)` on `onCreate` method, according to the list below:

```kotlin
class AppApplication: Application() {

    override fun onCreate() {
        super.onCreate()

        BeagleSetup().init(this)
    }
}
```

**Step 4: Update your Android Manifest.xml**

Finally, you have to update your `AndroidManifest.xml` again and define the `AppApplication` created as an application's initialization file. See the example below: 

```markup
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.beagleexamples">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:name=".AppApplication"
        android:usesCleartextTraffic="true"

        ..
```

{{%alert color="warning" %}}
  The `android:usesCleartextTraffic="true"` property of the application is enabled do allow local requests, because it allows to perform network traffic in plain text, such as HTTP
{{% /alert%}}

{{% alert color="warning" %}}
To do server-driven requisitons to a backend it's mandatory to customize the network layer. To know how, click here!
{{% /alert%}}

## **Other Customizations**
 * **Action:** You can create custom actions to be executed by your widgets according to the user interaction.
 * **Navigation Animation:** You can customize the enter and exit animations on Beagle navigation.
 * **Beagle Activity:** You can customize the activity that will be displayed when a server-driven screen was called.
 * **Network Client:** You can customize the network client to configure in a way Beagle will make requests.
 * **Cache:** You can customize the cache to save server-driven screens.
 * **Loading and errors treatment:** You can customize the error treatment and loading of server-driven screens.
 * **Proguard:** You can optimize and obfuscate your code using Proguard, all settings are already set internally. If you want to create custom components or change any Proguard's rules, Beagle supports it.
 * **Deep Link handler:** You can configure the Deep Link Handler to navigate from a server-driven screen to a native screen.
 * **Image Downloader:** You can customize how images will be downloaded to be displayed by Beagle.
 * **Design System:** You can customize your design system to build more beautiful screens and Beagle will display them.
 * **Serializer and Deserializer:** You can customize how Beagle will serialize and deserialize.
 * **Log:** You can customize how the logs generated by Beagle will be displayed.
 * **Widget:** You can create more specific components for your project and you can use them to build your server-driven screens.
 * **Operations:** You can create your operations to easily manipulate context by your backend.
 * **Analytics:** You can configure the analytics to generate screen and actions reports. 
