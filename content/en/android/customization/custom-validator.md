---
title: Custom Validator
weight: 111
description: >-
  You will find here a description on how to validate a form. This component is
  deprecated.
---

---

{{% alert color="danger" %}}
This component does not work on 1.1.0 version and it will be removed in the future version. Use [**SimpleForm**]({{< ref path="/api/components/forms/simple-form" lang="en" >}}) instead.
{{% /alert %}}

## Introduction

A **validator** component is used when it is necessary to validate some form information. It validates through `isValid`method that is requested when a `FormSubmit` or a `notifyChanges()` are implemented. This validator is listed as component that receives data in a form named `FormInput.`

## Validator context

The **Form** component capture data informed by the user and validates and send them to BFF. This validation can happen when the user activates FormSubmit or when notifies its status to `InputWidget(FormInput).`

**Validator** receives two generic parameters and it has only on isValid method that returns a boolean value.

```kotlin
interface Validator<in I, in W> {
    fun isValid(input: I, widget: W): Boolean
}
```

- **input:** represents the value `inputWidget` sends as data to be validated.
- **widget:** refers to `InputWidget` that sent the data.

## Implementing a validator

To implement a validator, you have to have a form that enables **FormSubmit** when **FormInput** is not empty. In this case it will receive a string with the data and then it will be validated if it is empty or not.

- The class that will validate the data is annotated as `@RegisterValidator`
- This annotation registers this validator on Beagle and identifies it by the name, listed in parentheses.

```kotlin
@RegisterValidator("text-is-not-blank")
class TextNotBlankValidator : Validator<String, ServerDrivenComponent> {
    override fun isValid(input: String, widget: ServerDrivenComponent): Boolean {
        return !input.isBlank()
    }
}
```

- To send this validation to `InputWidget`, it is necessary to refer the given name to the annotation above `"text-is-not-blank"`in the component. Follow the example below:

```kotlin
FormInput(
    name = "required-field",
    required = true,
    validator = "text-is-not-blank",
    child = SampleTextField(placeholder = "placeholder")
)
```

The follow example, the `InputWidget` implementation will run the Validator calling the **`notifyChanges()`** method.

```kotlin
@RegisterWidget
class SampleTextField(private val placeholder: String) : InputWidget() {

    @Transient
    private lateinit var textFieldView: EditText

    override fun getValue() = textFieldView.text.toString()

    override fun onErrorMessage(message: String) {
        textFieldView.error = message
    }

    override fun toView(context: Context) = EditText(context).apply {
        textFieldView = this
        textFieldView.hint = placeholder
        textFieldView.isSingleLine = true
        doOnTextChanged { _, _, _, _ -> notifyChanges() }
    }
}
```

{{% alert color="info" %}}
If you want to **validate** the information **as the user types it**, the suggestion is to call the function `notifyChanges()` inside the other function `doOnTextChange{}.`
{{% /alert %}}
