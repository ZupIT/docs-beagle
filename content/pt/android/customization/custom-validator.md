---
title: Validador customizado
weight: 127
description: >-
  Nesta seção, você encontra a descrição de como validar um formulário. Este
  componente foi depreciado
---

---

{{% alert color="danger" %}}
Este componente foi descontinuado na versão 1.1.0 e será removido em uma versão futura. Use [**SimpleForm**]({{< ref path="/api/components/forms/simple-form" lang="pt" >}}) em seu lugar
{{% /alert %}}

## Introdução

Um componente do tipo **`Validator`** é utilizado quando for necessário validar alguma informação em um formulário. Essa validação acontece através do método `isValid`, que é chamado sempre que são executados um`FormSubmit` ou um `notifyChanges()`.

Além disso, esse **Validator** é listado no componente que recebe dados em um formulário, ao qual chamamos de`FormInput.`

## Contexto do validador

No Beagle, é possível usar o componente **Form** para capturar dados informados pelo usuário, validá-los e enviá-los ao [**BFF**]({{< ref path="/key-concepts#backend-for-frontend" lang="pt" >}}).

Essa validação pode tanto ocorrer quando o usuário ativa o FormSubmit e/ou quando o usuário notifica que o estado de seu `InputWidget(FormInput)` mudou, como por exemplo, quando ditamos um texto ou quando o campo perde o foco.

O **Validator** recebe dois parâmetros genéricos e possui um único método `isValid` que retorna um valor booleano.

```kotlin
interface Validator<in I, in W> {
    fun isValid(input: I, widget: W): Boolean
}
```

- **input:** Representa o valor que o `inputWidget` envia como informação \(data\) a ser validada.
- **widget:** Widget que referencia o `InputWidget` que enviou a informação \(data\).

## Implementando um validador

É possível implementar um validador quando, por exemplo, você tem um formulário que habilite o **FormSubmit** apenas nos casos em que o **FormInput** não estiver vazio. Quando isso acontece, o Form deve receber uma String com a entrada de dados e será validada se está vazia ou não.

- A classe que validará a informação está anotada com um `@RegisterValidator`
- Essa anotação registra esse validador no Beagle e o identifica pelo nome listado entre parênteses.

```kotlin
@RegisterValidator("text-is-not-blank")
class TextNotBlankValidator : Validator<String, ServerDrivenComponent> {
    override fun isValid(input: String, widget: ServerDrivenComponent): Boolean {
        return !input.isBlank()
    }
}
```

- Para enviar ao `InputWidget` essa validação, é necessário somente referenciar o nome dado na anotação acima `"text-is-not-blank"`no componente.

Siga o exemplo abaixo:

```kotlin
FormInput(
    name = "required-field",
    required = true,
    validator = "text-is-not-blank",
    child = SampleTextField(placeholder = "placeholder")
)
```

Já no exemplo a seguir, a implementação de um `InputWidget` irá executar o Validator chamando o método **`notifyChanges()`**.

```kotlin
@RegisterWidget
class SampleTextField(private val placeholder: String) : InputWidget() {

    private lateinit var textFieldView: EditText

    override fun getValue() = textFieldView.text.toString()

    override fun onErrorMessage(message: String) {
        textFieldView.error = message
    }

    buildView(rootView: RootView) = EditText(rootView.getContext()).apply {
        textFieldView = this
        textFieldView.hint = placeholder
        textFieldView.isSingleLine = true
        doOnTextChanged { _, _, _, _ -> notifyChanges() }
    }
}
```

{{% alert color="info" %}}
Se você deseja **validar** a informação **conforme o usuário a digita**, a sugestão é que você chame a função`notifyChanges()` dentro da função `doOnTextChange{}`.
{{% /alert %}}
