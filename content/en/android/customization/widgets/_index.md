---
title: Custom Widget
weight: 129
description: >-
  You will find here an example on how to create a component and a customized
  widget
---

---

## Introduction

Beagle already has **basic widgets** used to create interface components of your server-driven application. 

However, your application may need more customized components \(Custom Views\), and to make them 'visible' to Beagle, you have to create a customized widget. Because of that, you can create custom widgets that will be visible / rendered on Beagle







{{% alert color="danger" %}}
It is **mandatory** to add the `@Transient` tag for **all attributes** present in the classes that will represent the widgets so that they are not taken into account in the component serialization and deserialization.
{{% /alert %}}
