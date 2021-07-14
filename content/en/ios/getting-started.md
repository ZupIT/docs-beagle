---
title: Getting Started
weight: 120
description: In this section, you'll learn how to create an iOS project with Beagle
---

**Topics covered**

* Creating an iOS project from scratch
* Adding Beagle to your project (works with existing projects)
* Configuring Beagle
* Other customizations

**Requirements**

* MacOS with Xcode 11+ installed
* Swift Package Manager (already installed with Xcode), Cocoapods or Carthage
* iOS 10.0+
* Swift 5.0+


## Creating an iOS project from scratch

<br>

We're using Xcode 11+ on this example, so if you don't have it, please download it from AppStore.

**Step 1:** Open your Xcode and click on _File -> New -> Project_.

**Step 2:** Select a single view application known as just _App_ and click on next.

**Step 3:** Name your project, select the interface as _Storyboard_, language _Swift_ and go next.

**Step 4:** Inform where you want to place your project and confirm.

## Adding Beagle to your project

<br>

You can install Beagle using SPM (Swift Package Manager), Cocoapods or Carthage. Just pick one and follow the instructions bellow:

<br>

Current Beagle's version: [![badge](https://img.shields.io/cocoapods/v/Beagle)](https://cocoapods.org/pods/Beagle)

{{< tabs id="T1" >}}
{{% tab name="Swift Package Manager" %}}

<br>

You only need Xcode here. More on Swift Package Manager: <https://swift.org/package-manager>

**Step 1:** Click on _File -> Swift Packages -> Add Package Dependency_.

**Step 2:** Enter Beagle's package repository url: `https://github.com/ZupIT/beagle.git` and click on next.

**Step 3:** Confirm Beagle's version, click on next and let Xcode resolve the package.

**Step 4:** On the final dialog, update Beagle’s _Add to Target_ column with the target that should import `Beagle`.

{{% alert color="success" %}}
You've successfully installed Beagle on your project using SPM! 
{{% /alert %}}

<br>

### SPM with existing Package.swift

<br>

**Step 1:** Add Beagle to dependencies choosing preferably the latest version available, like 1.8.0 for instance.
```swift
dependencies: [
  .package(name: "Beagle", url: "https://github.com/ZupIT/beagle.git", from: "${beagle.version}"}}),
]
```
**Step 2:** Add Beagle as a dependency of your application target:
```swift
targets: [
  .target(name: "MyApp", dependencies: ["Beagle"], path: "Sources")
]
```
{{% alert color="success" %}}
Now Beagle is a part of your package.swift.
{{% /alert %}}

{{% /tab %}}

{{% tab name="Cocoapods" %}}

<br>

You'll need Cocoapods installed and Terminal.

**Step 1:** Navigate to your project's root folder and run the command `pod init` that will create a _Podfile_.

**Step 2:** Edit your _Podfile_, adding Beagle's pod name, like the code below:
```ruby
target 'MyApp' do
  pod 'Beagle'
end
```

**Step 3:** Run the command `pod install` and the dependencies will be resolved in a Workspace.

**Step 4:** Now, open the Workspace created for you. From now you'll use only the Workspace.

{{% alert color="success" %}}
You've successfully installed Beagle on your project using Cocoapods! 
{{% /alert %}}

{{% /tab %}}

{{% tab name="Carthage" %}}

<br>

To do this, you'll need Carthage installed and a Terminal.

**Step 1:** Navigate to your project's root folder and run `touch Cartfile` to create a _Cartfile_ for us.

**Step 2:** Open the _Cartfile_ and edit it with our repository link like this `github "ZupIT/beagle" ~> ${beagle.version}`, choosing preferably the Beagle's latest version available.

**Step 3:** Now, check your Xcode version and follow the instructions below:

* For Xcode 12+, you need Carthage v0.37+. Then you can run the command `carthage udpate --use-xcframeworks`. This command will create _xcframeworks_. 

* For Xcode below 12, just run `Carthage update` and it will create _xcframeworks_.

**Step 4:** Now, go to: your project's root folder -> Carthage -> Build. Pick up _Beagle.xcframework_ folder and drag into _Xcode -> yourProject.xcodeproj -> Targets -> yourProject -> General -> Frameworks, Libraries, and Embedded Content_.

**Step 5:** To run Beagle you will also need _YogaKit_. You can install it using SPM (Swift Package Manager).

1. From the File menu, navigate through Swift Packages and select '**Add Package Dependency**';
2. Enter package repository URL: `https://github.com/ZupIT/yoga.git`;
3. Confirm Beagle's version and let Xcode resolve the package.

{{% alert color="success" %}}
You've successfully installed Beagle on your project using Carthage! 
{{% /alert %}}
{{% /tab %}}
{{< /tabs >}}

## Configuring Beagle

<br>

After Beagle's installation, you need to configure it to run on our application. For that, follow the instructions below:

**Step 1:** Create a BeagleConfig class:

This class contains part of Beagle's initial configuration. You have to implement a _config_ static function in it to apply these configurations.

```swift
import Beagle
import Foundation

class BeagleConfig {
    static func config() {}
}
```

**Step 2:** On this function, create a constant called `dependencies` that must be a `BeagleDependencies`.

You’ll attribute to this constant some project’s configurations like for example, the base URL that will be combined with a relative URL to bring the JSON that will load your server-driven screen.

To configure it, use the example below:

```swift
import Beagle
import Foundation

class BeagleConfig {
    static func config() {
        let dependencies = BeagleDependencies()
        dependencies.urlBuilder = UrlBuilder(
            baseUrl: URL(string: "http://localhost")
        )
        Beagle.dependencies = dependencies
    }
}
```

**Step 3:** Now, we’ll configure the _SceneDelegate_ class so it can be used to initialize our application with Beagle from a screen through BFF:

1. Inside your _SceneDelegate.swift_ in the function `scene`, call our function `BeagleConfig.config()`.

2. Name the `guard let` that Xcode already created for us, as `windowScene`.

3. Initialize the variable `window` created outside the function scope, using our `windowScene`

4. Make the `window.rootViewController` the screen from BFF that you want to display.

5. Now just call the function `makeKeyAndVisible()` from the variable `window`.

Your code should be like this:

```swift
import UIKit
import Beagle

class SceneDelegate: UIResponder, UIWindowSceneDelegate {

    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        BeagleConfig.config()
        guard let windowScene = (scene as? UIWindowScene) else { return }
        window = UIWindow(windowScene: windowScene)
        window?.rootViewController = BeagleScreenViewController(.remote(.init(url: "/yourScreenRelativeURL")))
        window?.makeKeyAndVisible()
    }

}

```

{{% alert color="warning" %}}
Important: Beagle does not provide a default Network, Cache and Log layers. But without a Network layer it is impossible to make requisitions for JSON's.

So it's mandatory to create a Network layer so Beagle can comunicate with a BFF through requisitions.
{{% /alert %}}

You can create these layers using these tutorials:

[**Custom Network Layer**]({{< ref path="/resources/customization/beagle-for-ios/network-layer" lang="en" >}})
<br>

[**Custom Cache Manager**]({{< ref path="/resources/cache/how-to-configure-cache" lang="en" >}})
<br>

[**Custom Log System**]({{< ref path="/resources/customization/beagle-for-ios/log-system" lang="en" >}})

<br>

Or if you want to skip these configurations, you can use our support library: [**Beagle Scaffold**]({{< ref path="/get-started/using-beagle-helpers/ios/beagle-scaffold" lang="en" >}}), that contains default implementations of Network, Cache and Log layers. 

But remember that is important to customize your Network layer for security matters.

## Other Customizations

<br>

* **Action:** you can create custom actions to be executed by your widgets as your users interacts with your app
* **Navigation Animation:** you can customize Beagle screen navigation animations
* **Network Client:** you can customize the network client to configure the way Beagle will make requests
* **Cache:** you can customize your cache layer to save server-driven screens as you wish
* **Loading and error handling:** you can customize the error handling and loading of server-driven screens
* **Deep Link handler:** you can configure Deep Link Handler to navigate from a server-driven screen to a native screen
* **Image Downloader:** you can customize how images will be dowloaded to be displayed by Beagle
* **Design System:** you can customize your design system to build more beautiful screens to be displayed by Beagle
* **Serializer and Deserializer:** you can customize how Beagle will serialize and deserialize
* **Log:** you can customize how logs generated by Beagle will be displayed
* **Widget:** you can create more specific components to your project and use them to build your server-driven screens
* **Operations:** you can create your operations to easily manipulate the context by your backend
* **Analytics:** you can configure an analytics to generate screen and actions reports