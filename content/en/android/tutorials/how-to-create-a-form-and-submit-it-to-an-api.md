---
title: How to create a form and submit it to an API
weight: 233
description: "On this section, you will find how to create a form and submit it to an API."
---

---

## Introduction

For this tutorial, you'll use some [**actions**]({{< ref path="/api/actions/" lang="en" >}}) and APIs:

**Actions**

- [**SetContext**]({{< ref path="/api/actions/setcontext" lang="en" >}})
- [**SendRequest**]({{< ref path="/api/actions/sendrequest" lang="en" >}})

**APIs**

- [**Context**]({{< ref path="/api/context/" lang="en" >}})
- [**Binding**]({{< ref path="/api/context#bindings" lang="en" >}})
- [**Expressões**]({{< ref path="/api/context#bindings" lang="en" >}})

Despite the [**Server-Driven**]({{< ref path="/key-concepts#server-driven-ui" lang="en" >}}) concept of working with components separatedly, it is possible to stream information to an API. Check out below:

## Use example

On this example, use the **TextInput** and **Button** components to simulate the login screen.

### Step 1: Create a login screen

Here, you have to signal to Beagle that it has to render on the screen two TextInputs and one Button. This will represent the login screen.

{{< tabs id="T101" >}}
{{% tab name="JSON" %}}

```javascript
{
  "_beagleComponent_": "beagle:container",
  "children": [
    {
      "_beagleComponent_": "beagle:textInput",
      "placeholder": "Username"
    },
    {
      "_beagleComponent_": "beagle:textInput",
      "placeholder": "Password"
    },
    {
      "_beagleComponent_": "beagle:button",
      "text": "Login"
    }
  ]
}
```

{{% /tab %}}

{{% tab name="Kotlin DSL" %}}

```kotlin
Container(
     children = listOf(
                TextInput(placeholder = "Username"),
                TextInput(placeholder = "Password"),
                Button(text = "Login")
            )
        )
```

{{% /tab %}}
{{< /tabs >}}

### Step 2: Configure SetContext

Now use the [**SetContext**]({{< ref path="/api/actions/setcontext" lang="en" >}}) action that alters a **specific** [**context**]({{< ref path="/api/context" lang="en" >}}) on a screen. This is necessary to bring the screen to life and make the typed data on them sent to an API.

On this scenario, the context will keep the user name and password typed data, so this same data will be forwarded to an API.

Alter this code to add this SetContext logic:

{{< tabs id="T102" >}}
{{% tab name="JSON" %}}

```javascript
{
  "_beagleComponent_": "beagle:container",
  "children": [
    {
      "_beagleComponent_": "beagle:textInput",
      "value": "@{credentials.username}",
      "placeholder": "Username",
      "onChange": [
        {
          "_beagleAction_": "beagle:setContext",
          "contextId": "credentials",
          "value": "@{onChange.value}",
          "path": "username"
        }
      ]
    },
    {
      "_beagleComponent_": "beagle:textInput",
      "value": "@{credentials.password}",
      "placeholder": "Password",
      "onChange": [
        {
          "_beagleAction_": "beagle:setContext",
          "contextId": "credentials",
          "value": "@{onChange.value}",
          "path": "password"
        }
      ]
    },
    {
      "_beagleComponent_": "beagle:button",
      "text": "Login"
    }
  ],
  "context": {
    "id": "credentials",
    "value": {
      "username": "",
      "password": ""
    }
  }
}
```

{{% /tab %}}

{{% tab name="Kotlin DSL" %}}

```kotlin
data class Credentials(
  val username: String,
  val password: String
)

Container(
        context = ContextData(
             id = "credentials",
             value = Credentials(username = "",password = "")
            ),
         children = listOf(
             TextInput(
                  placeholder = "Username",
                  value = "@{credentials.username}",
                  onChange = listOf(
                      SetContext(
                          contextId = "credentials",
                          path = "username",
                          value = "@{onChange.value}"
                      )
                  )
              ),
              TextInput(
                  placeholder = "Password",
                  value = "@{credentials.password}",
                  onChange = listOf(
                      SetContext(
                           contextId = "credentials",
                           path = "password",
                           value = "@{onChange.value}"
                      )
                   )
                ),
               Button(text = "Login")
            )
        )
```

{{% /tab %}}
{{< /tabs >}}

On this example, the `onChange` event of the TextInput is always called when the user types a new text in the input. Inside the onChange, there is an implicit context [**implicit context**]({{< ref path="/api/context/#2-implicit-context" lang="en" >}}) called `onChange` where the value is the last typed `value` by the user.

#### OnChange

In the context, the action SetContext will be performed in each character typed by the user. In SetContext, it is required the `contextId`, because it will make reference to the context that it will be altered, besides of the `value` that represents the new value, and the `path` that is the way inside the context that will be altered.

#### Path

It is the only optional parameter, that means in case it does not pass, the SetContext will alter the root context in order to make `value` the new value. On the example above, 
it is clear that you want to alter only 
the `username` and the `password` key in their respective SetContexts.

The context and expressions combination is of great potential because they can be implemented inside the `Action` you refer in the implicit context of whom performed the actions. You could see that in the `onChange` case, but it is possible to make a reference to other contexts inside an implicit context, then use multiple expressions.

{{% alert color="info" %}}
You can start a screen with predefined context values. For that, it is possible that the screen render in these values using expressions.

On the example above, you started the `username` and `password` fields with empty values coming from the context.
{{% /alert %}}

### Step 3: Send data to an API

You already have "saved" these context values so that on this step you can send it to an API through a request.

{{% alert color="warning" %}}
This step does not have any visual difference from the last step.
{{% /alert %}}

To finish the Login flow, you will now add an [**Action**]({{< ref path="/api/actions" lang="en" >}}) called [**SendRequest**]({{< ref path="/api/actions/sendrequest" lang="en" >}}), that basically allows you to make a HTTP request, and on the `onSuccess` case, show an [**Alert**]({{< ref path="/api/actions/alert" lang="en" >}}) welcoming the user.

{{< tabs id="T103" >}}
{{% tab name="JSON" %}}

```javascript
{
  "_beagleComponent_": "beagle:container",
  "children": [
    {
      "_beagleComponent_": "beagle:textInput",
      "value": "@{credentials.username}",
      "placeholder": "Username",
      "onChange": [
        {
          "_beagleAction_": "beagle:setContext",
          "contextId": "credentials",
          "value": "@{onChange.value}",
          "path": "username"
        }
      ]
    },
    {
      "_beagleComponent_": "beagle:textInput",
      "value": "@{credentials.password}",
      "placeholder": "Password",
      "onChange": [
        {
          "_beagleAction_": "beagle:setContext",
          "contextId": "credentials",
          "value": "@{onChange.value}",
          "path": "password"
        }
      ]
    },
    {
      "_beagleComponent_": "beagle:button",
      "text": "Login",
      "onPress": [
        {
          "_beagleAction_": "beagle:sendRequest",
          "url": "http://beagle.free.beeceptor.com/login",
          "method": "POST",
          "headers":{
               "Content-Type":"application/json"
          },
          "data": {
            "username": "@{credentials.username}",
            "password": "@{credentials.password}",
            "authType": "login"
          },
          "onSuccess": [
            {
              "_beagleAction_": "beagle:alert",
              "title": "You are logged-in",
              "message": "@{onSuccess.data.message}"
            }
          ]
        }
      ]
    }
  ],
  "context": {
    "id": "credentials",
    "value": {
      "username": "",
      "password": ""
    }
  }
}
```

{{% /tab %}}

{{% tab name="Kotlin DSL" %}}

```kotlin
data class Credentials(
  val username: String,
  val password: String
)

data class RequestCredentials(
  val username: String,
  val password: String,
  val authType: String
)

Container(
        context = ContextData(
            id = "credentials",
            value = Credentials(username = "", password = "")
        ),
        children = listOf(
            TextInput(
                placeholder = "Username",
                value = "@{credentials.username}",
                onChange = listOf(
                    SetContext(
                        contextId = "credentials",
                        path = "username",
                        value = "@{onChange.value}"
                    )
                )
            ),
            TextInput(
                placeholder = "Password",
                value = "@{credentials.password}",
                onChange = listOf(
                    SetContext(
                        contextId = "credentials",
                        path = "password",
                        value = "@{onChange.value}"
                    )
                )
            ),
            Button(
                text = "Login",
                onPress = listOf(
                    SendRequest(
                        url = "http://beagle.free.beeceptor.com/login",
                        method = RequestActionMethod.POST,
                        headers = mapOf(
                            "Content-Type" to "application/json"
                        ),
                        data = RequestCredentials(
                          username = "@{credentials.username}",
                          password = "@{credentials.password}",
                          authType = "login"
                        ),
                     onSuccess = listOf(
                       Alert(
                         title = "You are logged-in",
                         message = "@{onSuccess.data.message}"
                     )
                 )
             )
           )
         )
      )
    )
```

{{% /tab %}}
{{< /tabs >}}

When running this code, you have to fill in the fields and press the "Login" button.

After that, the `onPress` event will perform SendRequest sending all the data that are inside the context with the `credentials` ID.

When the SendRequest it is done, the response is finished, the `onSuccess` event will perform an [**Action Alert**]({{< ref path="/api/actions/alert" lang="en" >}}) that it will show an alert with the returned data from the API.
