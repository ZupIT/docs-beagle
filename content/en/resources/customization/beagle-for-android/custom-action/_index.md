---
title: Custom Action
weight: 102
description: >-
  You will find here the description on how to create a Custom Action and
  details about its methods.
---

---

## Introduction

`CustomAction` is a Beagle's component that can be called through events triggered by other components, including `actions`.

Beagle already has some predefined actions, however it is possible to create custom actions.





{{% alert color="danger" %}}
It is **mandatory** to add the `@Transient` tag for **all attributes** present in the classes that will represent the actions so that they are not taken into account in the component serialization and deserialization.
{{% /alert %}}
