---
title: Criando um formulário e submetendo para uma Api
weight: 233
description: "Nesta seção, você confere como criar um formulário e submetê-lo a uma API."
---

---

## Introdução

Para este tutorial, iremos usar algumas [**ações**]({{< ref path="/api/actions" lang="pt" >}}) e também APIs:

**Actions**

- [**SetContext**]({{< ref path="/api/actions/setcontext" lang="pt" >}})
- [**SendRequest**]({{< ref path="/api/actions/sendrequest" lang="pt" >}})

**APIs**

- [**Context**]({{< ref path="/api/context/" lang="pt" >}})
- [**Binding**]({{< ref path="/api/context#binding" lang="pt" >}})
- [**Expressões**]({{< ref path="/api/context#binding" lang="pt" >}})

Apesar do conceito [**Server-Driven**]({{< ref path="/key-concepts#server-driven-ui" lang="pt" >}}) trabalhar componentes separadamente, é possível realizar a transmissão das informações para uma API e é o que vamos demonstrar abaixo.

## Exemplo de Uso

Neste exemplo, iremos utilizar os componentes [**TextInput**]({{< ref path="/api/components/ui/textinput" lang="pt" >}}) e [**Button**]({{< ref path="/api/components/ui/button" lang="pt" >}}) para simularmos uma tela de Login.

### Passo 1: Criar uma tela de Login

Até este momento, vamos apenas sinalizar ao Beagle de que ele deve renderizar na tela dois componentes TextInputs e um componente Button para representarmos a tela de Login.

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

### Passo 2: Configurar o SetContext

Agora utilize uma **ação** chamada [**SetContext**]({{< ref path="/api/actions/setcontext" lang="pt" >}}), que altera um [**contexto**]({{< ref path="/api/context" lang="pt" >}}) **específico** em uma tela. Isto é necessário para dar vida à tela e fazer com que os dados que forem digitados nela sejam enviados para uma API.

Neste cenário, o contexto é que irá guardar os dados digitados de nome do usuário e da senha para que, posteriormente, encaminhar estes dados para a API.

Altere o código para adicionar essa lógica do SetContext:

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

Neste exemplo, utilizamos o evento `onChange` do próprio TextInput que é chamado sempre que o usuário digita um texto novo no input. Dentro do onChange, existe um [**contexto implícito**]({{< ref path="/api/context#2-contextos-implícitos" lang="pt" >}}) chamado `onChange` no qual o `value` é o último valor digitado pelo usuário.

#### OnChange

Ainda dentro deste contexto, a ação de SetContext será executada a cada caractere digitado pelo usuário. Por isso que neste SetContext é obrigatório o `contextId`, que fará referência ao contexto que será alterado, além do `value` que representa o novo valor e do `path` que é o caminho dentro do contexto que iremos alterar.

#### Path

É o único parâmetro opcional. Isto significa que, caso ele não passe, o SetContext irá alterar a raiz do contexto para que o `value` seja o novo valor. No exemplo acima, estamos deixando claro que queremos alterar somente a chave `username` e `password` nos seus respectivos SetContexts.

O poder do contexto + expressões é bem grande, pois é possível que, dentro de uma Action, você referencie o contexto implícito de quem a executou. No exemplo que vimos, é o caso do `onChange`, mas é possível fazer referência a outros contextos dentro de um contexto implícito, assim como usar múltiplas expressões.

{{% alert color="info" %}}
Você pode inicializar uma tela com valores pré-definidos no contexto. Com isso, é possível que a tela renderize esses valores utilizando expressões.

No exemplo visto acima, inicializamos os campos `username` e `password` com valores vazios vindo do contexto.
{{% /alert %}}

### Passo 3: Enviar dados para uma API

Até o momento, o que fizemos foi "salvar" estes valores no contexto para que, a partir desta etapa, possamos enviá-los para uma API por meio de uma requisição.

{{% alert color="warning" %}}
Esta etapa não tem nenhuma diferença visual do passo anterior.
{{% /alert %}}

Para finalizar o fluxo de Login, vamos agora adicionar uma [**Action**]({{< ref path="/api/actions" lang="pt" >}}) chamada [**SendRequest**]({{< ref path="/api/actions/sendrequest" lang="pt" >}}) que, basicamente, permite fazer uma requisição HTTP e, no caso do `onSuccess`, mostrar um [**Alert**]({{< ref path="/api/actions/alert" lang="pt" >}}) dando boas-vindas ao usuário.

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

Ao executar este código, você deve preencher os campos e pressionar o botão "Login".

Feito isso, o evento `onPress` irá executar o SendRequest enviando os dados que estão dentro do Context com o ID `credentials`.

Assim que o SendRequest for feito e a requisição for concluída, o evento `onSuccess` irá executar a [**Action Alert**]({{< ref path="/api/actions/alert" lang="pt" >}}) que irá mostrar um alerta contendo os dados retornados da API.
