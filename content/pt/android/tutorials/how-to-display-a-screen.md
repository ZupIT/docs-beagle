---
title: Exibindo uma tela
weight: 224
description: >-
  Nesta seção, você encontra um exemplo prático de como exibir uma tela
  Server-Driven
---

---

## Exibindo uma tela a partir de uma URL

Para exibir uma tela **`full server-driven`**, você deve usar o método `this.newServerDrivenIntent<BeagleActivity>()` onde o `this` é a o contexto da sua tela atual.

Este método requer apenas o parâmetro `ScreenRequest`

Você deve seguir tal como o comando abaixo:

{{% alert color="warning" %}}
Certifique-se de listar a sua Beagle Activity assim como nomeada em seu AndroidManifest.

No exemplo abaixo a chamamos somente de &lt;BeagleActivity&gt;. Esse nome é você quem define ao criar sua BeagleActivity. Em nosso tutorial [**Criando um projeto do zero** ]({{< ref path="/get-started/creating-a-project-from-scratch/case-android" lang="pt" >}}), chamamos ela de `AppBeagleActivity`
{{% /alert %}}

```kotlin
val intent = this.newServerDrivenIntent<BeagleActivity>(ScreenRequest("/screen"))
startActivity(intent)
```

{{% alert color="info" %}}
Para testar o comando acima, o componente `ScreenRequest` requer somente uma URL que traga o JSON com as informações da tela server-driven que você quer exibir.

Neste caso, siga estes passos:

- Use o exemplo de tela no link abaixo. Ele traz um JSON de uma tela simples para teste. Basta copiar e colar no lugar da URL `"/screen"` . [https://run.mocky.io/v3/73322be2-96bf-467b-b6dc-e3fff179852c](https://run.mocky.io/v3/73322be2-96bf-467b-b6dc-e3fff179852c)
- Veja como fica o código abaixo.

```text
val intent = this.newServerDrivenIntent<BeagleActivity>(ScreenRequest("https://run.mocky.io/v3/73322be2-96bf-467b-b6dc-e3fff179852c"))
startActivity(intent)
```

{{% /alert %}}

### A classe Screen Request.

A`ScreenRequest` é uma classe interna do Beagle utilizada para solicitar qual tela você deseja exibir. Você listará apenas o atributo URL referente a página que deseja carregar vinda do BFF.

No entanto, esse elemento possui outros atributos, que podem ser utilizados na transição e entre telas. Para saber mais sobre essa classe, veja em [**Screen Request**]({{< ref path="/api/screen-request" lang="pt" >}})

## Exibindo uma tela a partir de um JSON

Para renderizar qualquer componente do Beagle no Android, é necessário informar um `viewGroup` onde a view correspondente ao componente será renderizada, como um `FrameLayout` dentro de uma `Activity`, `Fragment` ou `DialogFragment`. Veja o exemplo abaixo:

```markup
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/frame_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"/>
```

Agora chame o método `loadView()` a partir do `frame_layout` criado no `xml` e passe a `Activity` e o `JSON` como parâmetros.

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        frame_layout.loadView(
            activity = this,
            screenJson = "JSON AQUI"
        )
    }
}
```

{{% alert color="info" %}}
O método `loadView()` também pode receber como parâmetro um `Fragment` no lugar da `Activity` e dois outros opcionais, seguindo o exemplo abaixo:

```kotlin
loadView(
  fragment = seuFragment, 
  screenJson = "JSON AQUI",
  screenId = "seu identificador",
  shouldResetContext = seuBoolean,
)
```

### Parâmetros opcionais
`screenId` : representa um identificador de tela para criar o analytics quando ela é criada. O valor padrão é vazio.
`shouldResetContext` : quando true, isso remove no momento da chamada da função `loadView` todos os dados de contexto vinculados ao proprietário do ciclo de vida. O valor padrão é false.

{{% /alert %}}
