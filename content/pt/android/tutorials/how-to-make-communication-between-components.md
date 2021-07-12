---
title: Como fazer comunicação entre componentes
weight: 218
description: >-
  Nesta seção, você encontra o passo a passo para configurar a comunicação entre
  componentes e o contexto
---

---

A **comunicação entre componentes** é uma forma de compartilhar informações com o uso de Observables que, de modo geral, emitem notificações sempre que uma mudança acontece com eles.

Abaixo, você verá como fazer a configuração dessa comunicação usando o [**Contexto, um componente do Beagle.** ]({{< ref path="/api/context/" lang="pt" >}})

## Configurando a comunicação entre componentes.

### O que vou comunicar?

Nesse exemplo, consumiremos o resultado de uma API pública que retorna valores de cotação das moedas no mercado ou _Currency Rates_ .

O endpoint abaixo retorna um JSON semelhante. Listamos somente os dados que vamos utilizar, mas vale dizer que a resposta desse JSON é bem maior.

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

## Como vou comunicar?

Uma função chamada [**SetContext**]({{< ref path="/api/actions/setcontext" lang="pt" >}}) é a responsável por atualizar os valores do componente contexto. Ela poderá ser chamada na tela se você quiser usar as informações que estiverem nesse contexto.

Para fazer a comunicação, basta seguir os próximos passos:

- Ao definir o contexto na estrutura Kotlin DSL no backend é necessário implementar uma classe que estruture as informações que serão recebidas no contexto.
- Utilizar duas estruturas para configurar e utilizar o contexto, sendo elas, uma classe para representar a informação da resposta da API e um Container para exibir essa informação em uma tela.

### Passo 1: Criar a classe Currency

De acordo com a resposta da API de exemplo, escrevemos a classe abaixo para estruturar os dados. Essa é a primeira estrutura:

```text
data class Currency(
    val eur: String,
    val brl: String,
    val cad: String,
    val usd: String
)
```

### Passo 2: Criar o Container

Criada a primeiro estrutura, é o momento de estruturar o Container que conterá os componentes que desejamos exibir. É nele que listaremos os componentes e também o **`*Contexto`** para esse grupo de componentes.

{{% alert color="info" %}}
É importante lembrar que o **`*Contexto`** , nesse caso, é a funcionalidade do Beagle que registra informações de um componente e que podem ser recuperadas em outro componente. Ele **não é** o _contexto\(Context\)_ das views no Android.
{{% /alert %}}

O Container está dividido em **2 partes principais:**

1. O `context :` É um objeto do tipo `ContextData` que será a variável responsável por conter as informações que você deseja transferir.
2. O `children:` É uma lista dos componentes que fazem parte desse container e que terão acesso ao contexto definido nele.

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

### O Container

A estrutura dessa página tem 1 título, 1 botão e 4 _`text views`_ que receberão as informações do contexto.

- O **título** é meramente ilustrativo para exemplificar a estrutura e hierarquia da página.
- O **botão** é utilizado para disparar a resposta da API e chamar a função `SetContext` para atribuir o resultado da resposta ao contexto.
- Os 4 **text views** receberão as informações vindas do contexto.

### O Botão e as funções `SendRequest` e **`SetContext`**

Para configurar o botão é preciso definir primeiro, a função que será acionada quando o botão for clicado, ou seja, você deve configurar o atributo `onPress` do botão. A função que deve ser chamada no clique é a `SendRequest` e ela será responsável por requisitar a resposta da API configurada nela.

Veja abaixo os parâmetros dessa função cujo exemplo está **no código do container acima.**

#### SendRequest

| Atributo  | Descrição                                                                                                                                   |
| :-------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| url       | Endpoint que responderá com as informações que desejamos definir no contexto.                                                               |
| method    | Define o _method_ do request. Nesse exemplo será o GET.                                                                                     |
| onSuccess | Define uma ação a ser realizada quando a requisição da API é bem sucedida. É aqui no onSuccess que será chamada de função **`SetContext.`** |

Quando a requisição da API recebe uma resposta, o método `SetContext` é chamado e é nele que atribuiremos o resultado da chamada da API ao contexto.

Veja abaixo os atributos do `SetContext`:

#### **SetContext**

| Atributo  | Descrição                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| :-------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| contextId | **Obrigatório**. O ID que foi definido no ContextData, que referencia esse contexto. No exemplo o **id** é `"MyContext"`                                                                                                                                                                                                                                                                                                                                                                    |
| path      | Este atributo pode ser implementando para definir uma path especializado. Neste exemplo o definimos como "currency" , que é como os valores serão requisitados do contexto.                                                                                                                                                                                                                                                                                                                 |
| value     | **Obrigatório**. Ele atribui valor aos atributos declarados no contexto de acordo com a resposta da API. Neste exemplo para recuperar o valor da API devemos construir esse valor da seguinte forma: `"@{onSuccess.data.rates.EUR}"`, ou seja, a parte `onSuccess.data` representa a informação recebida na resposta da API, e de acordo com o JSON que representa essa estrura o resultado pode ser acessado usando `.rates.EUR` por exemplo, que é a chave do valor que queremos acessar. |

### O texto

Temos 4 componentes visuais nesse exemplo, eles que exibirão as informações atribuídas ao contexto. E aqui você percebe como ocorre a comunicação entre componentes, no passo anterior enviamos uma informação para o contexto, que agora irá ser recuperada.

Aqui temos somente o atributo `text` desse componente textual, porque é ele que definirá a informação a ser exibida.

| Atributo | Descrição                                                                                                                                                                                                                                                                      |
| :------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| text     | Valor no formato String que será exibido na tela. Para recuperar o valor do contexto use a notação `"@{myContext.currency.eur}"`, ou seja, você deve definir o caminho para acessar as informações salvas no contexto utilizando o `"@{ID_DO_CONTEXTO+PATH+NOME_DO_ATRIBUTO}"` |

### A tela

E agora a tela está pronta! Foi configurado um exemplo de contexto e demonstrado como acontece a comunicação entre os componentes.

No final deste exemplo a sua tela deve estar assim:

![](/shared/screenshot_1594752619.png)
