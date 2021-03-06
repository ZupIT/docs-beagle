---
title: Simple Form
weight: 355
description: Descrição do componente Simple Form
---

---

## O que é?

O`simpleForm` é responsável por renderizar um formulário em tela.

A sua estrutura é representada como mostrado abaixo:

| Atributo | Tipo                                                             | Obrigatório | Definição                                                                       |
| :------- | :--------------------------------------------------------------- | :---------: | :------------------------------------------------------------------------------ |
| onSubmit | List &lt;[**Action**]({{< ref path="/api/actions" lang="pt" >}})&gt;                  |      ✓      | Array de ações que serão disparadas quando um formulário válido é enviado.     |
| onValidationError | List &lt;[**Action**]({{< ref path="/api/actions" lang="pt" >}})&gt;                  |      ✓      | Array de ações que serão disparadas quando um formulário é inválido.      |
| children | List&lt;[**ServerDrivenComponent**]({{< ref path="/api/components" lang="pt" >}})&gt; |      ✓      | Define a lista de componentes visuais que compoe o formulário \(server-driven\) |
| context  | [**ContextData**]({{< ref path="/api/context" lang="pt" >}})                          |             | Adiciona um contexto para o simple form                                         |

## Como usar?

Ao criar um formulário é importante entender dois passos respectivamente

- A relação e atualização entre os campos
- E o que acontece quando o butão Submit é presionado.

### Atualizando os campos

O componente TextInput é o campo em que o usuario ou o sistema preencherá com alguma informação, e é importante conhecer seus atributos para melhor utiliza-lo. Aqui utilizaremos um de seus elementos , que é a função **`onChange`**.

{{% alert color="info" %}}
Para mais informações sobre esse componente vá até os detalhes do [TextInput]({{< ref path="/api/components/ui/textinput" lang="pt" >}}).
{{% /alert %}}

#### OnChange

Essa função é parte do componente Text Input e observa as modificações feitas dentro do seu campo, ou seja, sempre que o valor for modificado, algo for digitado, apagado, etc, essa função é chamada e ativa uma **lista** de outras [**ações**]({{< ref path="/api/actions" lang="pt" >}}) para acontecer sempre que o valor mudar. É nessa lista que adicionamos uma ação [**SetContext**]({{< ref path="/api/actions/setcontext" lang="pt" >}}) para definir o valor do [**Contexto**]({{< ref path="/api/context/" lang="pt" >}}) do formulário e atualizar os valores que são mostrados no campo.

Veja abaixo como implementamos o nosso `SimpleForm`

{{< tabs id="T150" >}}
{{% tab name="JSON" %}}

<!-- json-playground:simpleform.json
{
  "_beagleComponent_":"beagle:simpleForm",
  "context":{
    "id":"myContext",
    "value":""
  },
  "onSubmit":[
    {
      "_beagleAction_":"beagle:alert",
      "title":"Data submited",
      "message":"The password is @{myContext}"
    }
  ],
  "children":[
    {
      "_beagleComponent_":"beagle:textInput",
      "value":"@{myContext}",
      "placeholder":"Type in your password",
      "onChange":[
        {
          "_beagleAction_":"beagle:setContext",
          "contextId":"myContext",
          "value":"@{onChange.value}"
        }
      ]
    },
    {
      "_beagleComponent_":"beagle:button",
      "text":"Click to Submit",
      "onPress":[
        {
          "_beagleAction_":"beagle:submitForm"
        }
      ]
    }
  ]
}
-->

{{% playground file="simpleform.json" language="pt" %}}
{{% /tab %}}

{{% tab name="Kotlin DSL" %}}

```kotlin
SimpleForm(
  context = ContextData(id = "myContext", value = ""),
  children = listOf(
    TextInput(
      value = "@{myContext}",
      placeholder = "Type in your password",
      onChange = listOf(
        SetContext(contextId = "myContext", value = "@{onChange.value}")
      )
    ),
    Button(text = "Click to Submit", onPress = listOf(SubmitForm()))
  ),
  onSubmit = listOf(
    Alert(
      title = "Data submited",
      message = "The password is " + "@{myContext}"
    )
  )
)
```

{{% /tab %}}
{{< /tabs >}}

### onSubmit

É um atributo do SimpleForm que executa uma lista de ações que são chamadas **quando um formuário válido é submetido**. Caso o formulário seja inválido, ele irá executar as listas de ações do atributo **onValidationError**.

Para submeter um formulário é preciso utilizar a ação SubmitForm e para chama-la basta somente implementa-la em um [**Botão**]({{< ref path="/api/components/ui/button" lang="pt" >}}) que seja parte do SimpleForm, ou seja, que esteja em sua lista de filhos.

Ao clicar nesse botão, o onSubmit é ativado e a lista de ações será executada. Essa lista de ações define o que deve acontecer com as informações do formulario: por exemplo, se elas serão enviadas para um backend \(através da ação [**sendRequest**]({{< ref path="/api/actions/sendrequest" lang="pt" >}})\), etc.

### onValidationError

É uma ação do SimpleForm que executa uma lista de ações que são chamadas **quando um formuário inválido é submetido**. Essa validação é realizada no componente `TextInput` a partir dos atributos **error** e **showError**.
