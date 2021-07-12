---
title: How to make communication between components
weight: 218
description: >-
  On this section, you'll find the step by step to configure the communication between components and context
---

---

The **communication between components** is a way to share information with the use of Observables that, in general, issue notifications when a change happen with them.

You will see below how to configure the communication between components using [**Context, a Beagle component.** ]({{< ref path="/api/context/" lang="en" >}})

## Configuring the communication between components

### What will I communicate?

On this example you will consume a public API result that returns exchange rates values on the market or Currency Rates.

The end point below returns a similar JSON. It is listed only the data you will use, but it important to mention that this JSON response is bigger.

- Endpoint: [**https://api.exchangeratesapi.io/latest?base=USD**](https://api.exchangeratesapi.io/latest?base=USD)

```text
{
    "rates": {
        "CAD": 1.3563421308,
        "BRL": 5.3397475505,
        "EUR": 0.8826904405,
        "USD": 1.0,
    },
    "base": "USD",
    "date": "2020-07-13"
}
```

## How will I communicate?

A function called [**SetContext**]({{< ref path="/api/actions/setcontext" lang="en" >}}) is responsible to update the context component values and it will be called on the screen when the you want to use the information on the context.

Follow the next steps on how to do it:

- To define a context on the Kotlin DSL structure in the backend it is necessary to implement a class that structures the information that will be received by the context.

- Use the two structures to configure and utilize the context, one is a class to represent the API response information and a Container to show this information on the screen.

### Step 1: Create a Currency class

According to the API response, you wrote the class below to structure the data. This is the first structure:

```text
data class Currency(
    val eur: String,
    val brl: String,
    val cad: String,
    val usd: String
)
```

### Step 2: Create a Container

Once you create the first structure, it's time to structure the Container that will contain all the components you want to show. You have to list the components and also the  **`Context`**` to this components group.
 
{{% alert color="info" %}}
It is important to remember that this `Context` is a Beagle's functionality that registers components information and that can be recovered in another component. It is not the view context of Android.
{{% /alert %}}

Container is divided into **two main parts:**

1. The `context :` It is a `ContextDataobject` that it will be the variable responsible to contain all the information you want to transfer.
2. The `children:` List of components that are part of this container and that it will have access to the context defined by it.

{{< tabs id="T90" >}}
{{% tab name="JSON" %}}

```typescript
{
  "_beagleComponent_" : "beagle:container",
  "children" : [ {
    "_beagleComponent_" : "beagle:text",
    "text" : "Currency Table in USD"
  }, {
    "_beagleComponent_" : "beagle:button",
    "text" : "Click to get the currency Table",
    "onPress" : [ {
      "_beagleAction_" : "beagle:sendRequest",
      "url" : "https://api.exchangeratesapi.io/latest?base=USD",
      "method" : "GET",
      "onSuccess" : [ {
        "_beagleAction_" : "beagle:setContext",
        "contextId" : "myContext",
        "value" : {
          "eur" : "@{onSuccess.data.rates.EUR}",
          "usd" : "@{onSuccess.data.rates.USD}",
          "brl" : "@{onSuccess.data.rates.BRL}",
          "cad" : "@{onSuccess.data.rates.CAD}"
        },
        "path" : "currency"
      } ]
    } ]
  }, {
    "_beagleComponent_" : "beagle:text",
    "text" : "EUR: @{myContext.currency.eur}"
  }, {
    "_beagleComponent_" : "beagle:text",
    "text" : "BRL: @{myContext.currency.brl}"
  }, {
    "_beagleComponent_" : "beagle:text",
    "text" : "CAD: @{myContext.currency.cad}"
  }, {
    "_beagleComponent_" : "beagle:text",
    "text" : "USD: @{myContext.currency.usd}"
  } ],
  "context" : {
    "id" : "myContext",
    "value" : {
      "eur" : "",
      "usd" : "",
      "brl" : "",
      "cad" : ""
    }
  }
}
```

{{% /tab %}}

{{% tab name="Kotlin" %}}

```kotlin
Container(
    context = ContextData(
        id = "myContext",
            value = Currency(
                    eur = "",
                    brl = "",
                    cad = "",
                    usd = ""
            )
        ),
    children = listOf(
        Text("Currency Table in USD"),
        Button(
            text = "Click to get the currency Table",
            onPress = listOf(
                SendRequest(
                    url = "https://api.exchangeratesapi.io/latest?base=USD",
                    method = RequestActionMethod.GET,
                    onSuccess = listOf(
                        SetContext(
                            contextId = "myContext",
                            path = "currency",
                            value = Currency(
                                eur = "@{onSuccess.data.rates.EUR}",
                                brl = "@{onSuccess.data.rates.BRL}",
                                cad = "@{onSuccess.data.rates.CAD}",
                                usd = "@{onSuccess.data.rates.USD}"
                            )
                        )
                    )
                )
            )
        ),
        Text(
            text = "EUR: " + "@{myContext.currency.eur}"
        ),
        Text(
            text = "BRL: " + "@{myContext.currency.brl}"
        ),
        Text(
            text = "CAD: " + "@{myContext.currency.cad}"
        ),
        Text(
            text = "USD: " + "@{myContext.currency.usd}"
        )
    )
)
```

{{% /tab %}}
{{< /tabs >}}

### The Container

The structure of this page has 1 title, 1 button and 4 text views that will receive the context information. Check out more details:

- The **title** is only to exemplify the structure and the page hierarchy.
- The **button** it is used to trigger the API response and to call the SetContext function to attribute the response result to the context.
- The **4 text views** will receive the information coming from the context.

### **`SendRequest`** e **`SetContext`** button and functions

To configure the button, first it is necessary to define the function that will be triggered when the button is clicked, you have to configure the `onPress` attribute. The function that must be called when clicked is the `SendRequest`, it is responsible for resquest the response from the API, already configured on it.

See below the function's parameters, which the example it is **on the container code above**.

#### SendRequest

| Attribute | Description                                                                                                                                |
| :-------- | :----------------------------------------------------------------------------------------------------------------------------------------- |
| url       | Endpoint that will respond with information we want to define in the context.                                                              |
| method    | Defines the request method. This example is GET.                                                                                           |
| onSuccess | Defines an action to be performed when the API request is successful. It is on the onSuccess that will call the **`SetContext`** function. |

When the API request receives a response, the `SetContext` method is called and you will assign the API result to the context.

See the the `SetContext` attributes below:

#### **SetContext**

| Attribute | Description |
| :--- | :--- |
| contextId | **Required**. The id was defined in the ContextData, it refers to this context.  On the example, the **id** is`"MyContext"`. |
| path | It can be implemented to define a specialized path. This example you defined it as "currency", this is how the values will be requested in the context. |
| value | **Required**. It gives values to the declared attributes in the context, according to the API response. On this example to recover the API values, you have to build this value like: `"@{onSuccess.data.rates.EUR}"`, meaning that, the `onSuccess.data` represents the received information of the API response, according to the JSON, the result can be accessed using `.rates.EUR` for example, that it is the key value you want to access. |

### Text

Here you have 4 visual components assigned to the context. You will notice that it is here that the communication between components happens . In the previous step, an information was sent to the context and now we will recover it.

We have only text attribute of this textual component, because it is this one that will define the information to be shown.

| Attribute | Description                                                                                                                                                                                                                                                                   |
| :-------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| text      | VString values that will be show on the screen. To recover the value of the context, use the `"@{myContext.currency.eur}"` context, meaning that you have to define the path to access the saved information in the context using `"@{ID_DO_CONTEXTO+PATH+NOME_DO_ATRIBUTO}"` |

### The screen

And now the screen is ready! It is configured a context example, showing what happens in the communication between components.

At the end your screen must look like this one:

![](/shared/screenshot_1594752619.png)
