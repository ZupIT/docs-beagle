---
title: Customized Serializer  Deserializer
weight: 134
description: >-
  In this section, you will find a description of how to create a Serializer /
  Deserializer using Beagle and details of the methods it implements.
---

# Serializer / Deserializer

## Introduction

Beagle offers a customized **`serializer/deserializer`** set through  a `BeagleTypeAdapter` interface. With this **`Adapter`** it is possible to map any class, including classes that represent **concrete types**, such as the **Integer** class. You can see the interface contract below:


```kotlin
interface BeagleTypeAdapter<T> {
    fun fromJson(json: String) : T
    fun toJson(type: T) : String
}
```

## Example

This example creates an **`adapter`** for the `Person` interface that is extended from the **`data class`** `PersonImpl` that has only a `name` **attribute** as a `String` type.

In order to create an adapter you must: 

1. Create a class that will be your adapter and **annotate** it with the keyword `RegisterBeagleAdapter`. In this example we named this class as **`PersonAdapter`** ;
2. Declare a class you want to **map** with this adapter. Here, `PersonImpl` data class is implemented as an example.;
3. Extend the `adapter` \(PersonAdapter\) class from the **`BeagleTypeAdapter`** `Interface`, informing the class to be mapped on;
4. When extending the interface you must implement the `fromJson` and `toJson methods`. Check out the example below:

```text
interface Person

data class PersonImpl(val name: String) : Person

@RegisterBeagleAdapter
class PersonAdapter : BeagleTypeAdapter<Person> {

    override fun fromJson(json: String): Person {
        val rootObject = JSONObject(json)
        return PersonImpl(rootObject.getString(KEY))
    }

    override fun toJson(type: Person): String {
        type as PersonImpl
        val rootObject = JSONObject()
        rootObject.put(KEY, type.name)
        return rootObject.toString()
    }
}
```

### FromJson method 

This method takes a JSONObject as a string and returns an instance of the mapped class \(Person\).

### ToJson method 

This method takes the mapped data type \(Person\) and returns a JSONObject as a string.
