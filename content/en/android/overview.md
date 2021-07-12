---
title: Overview
weight: 3
type: overview
description: 'Here you will find information about how to configure Beagle in a Android application'
---

---

![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-scaffold?color=green&label=Beagle-Scaffold)
![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-defaults?color=green&label=Beagle-Defaults)
![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)

<hr>

To start using Beagle, you may configure a project from scrap or use one of our supporting libraries

## Configuring a project from scrap
{{% alert color="warning" %}}
In this option you will add Beagle to an Android project and configure it manually. This mode is suitable for more experienced users who are already familiar with Beagle. But of course, nothing prevents this from being your first contact!

Go to the [**Getting Started**]({{< ref path="/android/getting-started.md" lang="en" >}}) section to configure Beagle in an Android application manually
{{% /alert %}}

## Using a support library

{{% alert color="warning" %}}
![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-scaffold?color=green&label=Beagle-Scaffold)

This library will define almost all the necessary settings to start using Beagle in your project. We advise using these libraries for people who have never used Beagle before. Go to the [Beagle-Scaffold]({{< ref path="/android/libs/beagle-scaffold.md" lang="en" >}}) section to configure Beagle in an Android application
{{% /alert %}}

{{% alert color="warning" %}}
![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-defaults?color=green&label=Beagle-Defaults)

This library will only define the classes that define the default HTTP client layer HttpClient, Logger and Cache that demonstrate the contract with Beagle. All other settings necessary for the Beagle to work will be done manually<br>All these classes are also available in the Scaffold library above. Go to the [Beagle-Defaults]({{< ref path="/android/libs/beagle-defaults.md" lang="en" >}}) section to configure Beagle in an Android application:
{{% /alert %}}