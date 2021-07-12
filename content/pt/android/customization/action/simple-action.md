
---
title: Ação Simples
weight: 104
description: Criando e executando uma ação customizada
---

**Tópicos abordados:**
 - O que é uma ação
 - Como criar uma ação 
 - Como Registrar uma ação
 - Como usar uma ação

**Requisitos:**
 - Ter um projeto com o Beagle Configurado


## O que é?

No Beagle, uma ação lida com os comportamentos \(funções\), que serão executadas em sua aplicação assim que um determinado evento for disparado. Essas ações podem ser padrão do próprio Beagle ou customizadas.

Veja as ações padrões do Beagle na seção [**tipos de ações.**]({{< ref path="/api/actions/overview#tipos-de-ações" lang="pt">}}).

## Como criar uma ação

Para criar sua ação personalizada siga os seguintes os passos:

Passo 1. Criar uma classe anotada com `@RegisterAction`, e implementar a interface `Action`;
Passo 2. Colocar o nome da ação por parâmetro da annotation para evitar possíveis problemas com o Proguard;
Passo 3. Depois disso, a interface solicitará que o método `execute` seja implementado;
Passo 4. Agora, declare o resultado da `action`.

O atributo  `value`  é um exemplo de parâmetro que pode ser declarado no construtor dessa classe, você pode usar quantos precisar. O exemplo a seguir mostra uma ação com **Toast** recebendo um texto como parâmetro:

```kotlin
@RegisterAction("customActionAndroid")
data class CustomActionAndroid(
    val value: String
) : Action {
    override fun execute(rootView: RootView) {
        Toast.makeText(
            rootView.getContext(), 
            value, 
            Toast.LENGTH_SHORT
        ).show()
    }
}
```

## Como registrar sua ação

Existem duas formas de registrar seu componente.

 - Utilizando o Annotation Processor:

    Para isso, anote a classe da sua action com `@RegisterAction("className")` onde className é o nome da sua classe

 - Sem utilizar o Annotation Processor

## Como usar sua ação

Veja abaixo um exemplo de Action em um componente do tipo `Botão`:

```kotlin
Button(
   text = text,
   styleId = styleId,
   onPress = listOf(
      CustomActionAndroid("Sou uma ação customizada")
   )
)
```
