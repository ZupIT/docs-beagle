---
title: Adicionando um componente Beagle Server-driven
weight: 228
description: >-
  Esta seção descreve como adicionar um componente Server Driven a uma tela
  nativa
---

---

É possível que alguns componentes de uma tela que sejam exibidos via server-driven \(tela nativa\) recebam somente **uma seção da página** , ou mesmo um **único componente do servidor BFF**.

No exemplo abaixo, vamos exibir um botão server-driven em uma tela nativa. O resultado que deve aparecer na sua aplicação é esse:

<div align="center">
{{< figure src="/shared/screenshot_1594300176.png" width="20%" >}}
</div>

## Pré-requisitos

Para que essa configuração funcione corretamente, você precisa de:

- Um [**BFF**]({{< ref path="/key-concepts#backend-for-frontend" lang="pt" >}}) configurado com o Beagle. Caso não o tenha, veja como configurar nesse [**tutorial**]({{< ref path="/backend/" lang="pt" >}})creating-a-project-from-scratch/case-backend" lang="pt" >}}).
- Um frontend configurado com o Beagle para Android. Caso não o tenha, siga o nosso tutorial para configurar um projeto [**Android**]({{< ref path="/android/getting-started.md" lang="pt" >}})

## Passo 1: Criar o componente no backend

Neste exemplo, criaremos um botão como componente server-driven para que possamos exibí-lo em uma tela nativa.

Para isso, siga os passos a seguir:

1. Crie uma classe Kotlin em seu backend.
2. Nomeie-o `SingleComponentBuilder`. Essa será a classe que vai construir o seu componente \(objeto\) server-driven.
3. Feito isso, copie e cole o código abaixo.

```kotlin
import br.com.zup.beagle.widget.action.Alert
import br.com.zup.beagle.widget.ui.Button

class SingleComponentBuilder() {

  fun createButton(): Button {
    return Button(
        "I'm a server-based button",
        onPress = listOf(
            Alert(
                "Server-driven Button",
                "I'm a server-based button",
                labelOk = "OoooK"
            )
        )
    )
  }
}
```

{{% alert color="info" %}}
Este botão terá uma ação do tipo `"Alert"`, que já está configurada no código acima.
{{% /alert %}}

## Passo 2: Atualizar a classe Service e Controller no backend

Depois que você criou o componente, será preciso atualizar algumas classes. Veja como fazer essa configuração de acordo com cada uma delas.

### Atualizando o Service

O `Service` é a classe responsável por configurar o serviço que usaremos, que pode ser de vários tipos. No caso desse exemplo, só será listado o que está sendo configurando.

Comece abrindo a classe `ScreenBeagleService.kt` \(nome escolhido para o tutorial de configuração do `BFF`, mas você pode renomeá-la se quiser\).

Feito isso, copie a linha abaixo e cole dentro da classe `ScreenBeagleService`:

- _`fun createServerDrivenComponent() = SingleComponentBuilder().createButton()`_

```kotlin
import com.example.bff.builder.SingleComponentBuilder
import org.springframework.stereotype.Service

@Service
class ScreenBeagleService {
    fun createServerDrivenComponent() = SingleComponentBuilder().createButton()
}
```

### Atualizando Controller

Se a configuração acima funcionar, você atualizará o controller e o componente e eles estarão prontos para serem enviados ao frontend, caso haja uma requisição.

Agora, na classe controller, você deve chamar o serviço que acabamos de configurar e definir a URL que será o endpoint do componente criado.

Para isso, siga os passos:

1. Abra a classe`ScreenController`. Esse nome foi dado a essa classe no tutorial, ela que é responsável por expor os componentes via API REST.
2. Adicione outra função à essa classe \(Lembrando que a classe já tem algumas funções implementadas\).
3. Anote a função com o `@GetMapping()`e listar o endpoint `(Path relativo da URL)` que identifica esse componente.

Veja como deve ficar a configuração:

```kotlin
@RestController
class ScreenController(
        private val screenBeagleService: BeagleService
) {

  @GetMapping("/serverDrivenComponent")
  fun getServerDrivenComponent() = screenBeagleService.createServerDrivenComponent()
}
```

### Teste o endpoint

Agora que o componente esta criado e configurado, você deve testar o endpoint e verificar a resposta JSON. Para isso, realize essas ações:

1. Inicie a sua aplicação no backend \(Lembre-se que sua aplicação Backend está configurada para apresentar as resposta no localhost\).
2. Abra o um browser e acesse o endpoint: [http://localhost:8080/serverDrivenComponent](http://localhost:8080/serverDrivenComponent)

Você deve receber o JSON abaixo:

{{< tabs id="T96" >}}
{{% tab name="JSON" %}}

```typescript
{
  "_beagleComponent_" : "beagle:button",
  "text" : "I'm a server-based button",
  "onPress" : [ {
    "_beagleAction_" : "beagle:alert",
    "title" : "Server-driven Button",
    "message" : "I'm a server-based button",
    "labelOk" : "OoooK"
  } ]
}
```

{{% /tab %}}

{{% tab name="Kotlin DSL" %}}

```kotlin
Button(
    "I'm a server-based button",
    onPress = listOf(
        Alert(
            "Server-driven Button",
            "I'm a server-based button",
            labelOk = "OoooK"
        )
    )
)
```

{{% /tab %}}
{{< /tabs >}}

## Passo 3: Exibir o componente server-driven

Depois dos dois passos anteriores, o seu componente está pronto. Agora, você só precisa exibi-lo em uma tela nativa.

Para o Android você deve utilizar o `frame layout` para "receber" o componente do BFF e, assim, exibi-lo em uma tela Android nativa.

### Como receber um componente server driven no Android?

- Crie o arquivo `.XML` como no exemplo abaixo, que representa uma tela nativa com um título e um frame layout. Nesse exemplo definimos essa pagina como nossa `MainActivity`
- Depois, copie e cole a configuração abaixo:

```markup
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tv_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="100dp"
        android:text="Sou componente Nativo!"
        android:textSize="30sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <FrameLayout
        android:id="@+id/fr_server_driven"
        android:layout_width="match_parent"
        android:layout_height="40dp"
        app:layout_constraintTop_toBottomOf="@id/tv_title"
        android:layout_marginTop="40dp"
        />

</androidx.constraintlayout.widget.ConstraintLayout>
```

{{% alert color="info" %}}
Após configurar o frame layout, é preciso informar ao Beagle qual componente será exibido. Para isso, utilize a função `loadView` como listado no exemplo a seguir.
{{% /alert %}}

## O que é um loadView?

O método `loadView` é responsável carregar um conteúdo beagle dentro de sua view nativa.

A estrutura do **loadView** é:

| **Atributo**  | **Tipo**                                                            | Obrigatório | **Definição**                                                          |
| :------------ | :------------------------------------------------------------------ | :---------: | :--------------------------------------------------------------------- |
| activity      | AppCompatActivity/Fragment                                          |      ✓      | Define a activity ou fragment que estamos usando o loadview            |
| screenRequest | [**ScreenRequest**]({{< ref path="/api/screen-request" lang="pt" >}})                   |      ✓      | Define os parâmetros para a chamada da tela remota                     |
| listener      | **OnServerStateChanged = (serverState: ServerDrivenState) -> Unit** |             | Define o o listener que configura os callbacks para os estados da tela |

<br />

Veja aqui como fazer isso:

1. Abra a activity onde você deseja exibir a sua tela nativa.
2. Crie uma variável do tipo FrameLayout que deve receber a FrameLayout View que configuramos.
3. Agora basta chamar a função `.loadView` a partir da variável `frameLayout`

```kotlin
val frameLayout = findViewById<FrameLayout>(R.id.fr_server_driven)
frameLayout.loadView(this, ScreenRequest("/serverDrivenComponent"))
```

E pronto: basta iniciar sua aplicação e você verá a tela a seguir!

<div align="center">
{{< figure src="/shared/server-driven-comp-ios.gif" width="20%" >}}
</div>

Clique no botão e perceba que a função nesse componente está implementada e funcional, ou seja, o Beagle exibe todos os componentes como se fossem nativos.

