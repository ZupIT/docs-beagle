---
title: Serializador & Deserializador
weight: 123
description: >-
  Nesta seção, você encontra a descrição de como criar um
  Serializador/Deserializador usando Beagle e detalhes dos métodos que ele
  implementa.
---

# Serializador/Deserializador customizado

## Introdução

O **Beagle** oferece um **`serializador/deserializador`** customizado por meio da **Interface** `BeagleTypeAdapter.` Com esse **`Adapter`** é possível mapear uma classe qualquer, inclusive as que representam **tipos concretos**, como um **Integer**. 

Veja abaixo o contrato definido da interface:

```kotlin
interface BeagleTypeAdapter<T> {
    fun fromJson(json: String) : T
    fun toJson(type: T) : String
}
```

## Exemplo

Neste exemplo cum `adapter` foi criado para a interface `Person` que é extendida a partir da **`data class`** `PersonImpl` que possui somente um **atributo** `name` do tipo `String`. 

Para criar um **`adapter`** você deve:

1. Criar uma classe que será seu `adapter` e anotá-la com `@RegisterBeagleAdapter.`Neste exemplo o nome dado foi **`PersonAdapter`**
2. Declarar qual classe se quer mapear com esse adapter. Aqui mapeamos a interface **`Person`** que representa a `data class PersomImpl.`
3. Estender a classe `adapter` \(**`PersonAdapter`**\) a partir da **Interface** `BeagleTypeAdapter` informando a classe que você quer mapear;
4. Ao estender a interface você deverá implementar os métodos `fromJson` e `toJson`, veja o exemplo abaixo:

```text
interface Person

data class PersonImpl(val name: String) : Person

@RegisterBeagleAdapter
class PersonAdapter : BeagleTypeAdapter<Person> {

    override fun fromJson(json: String): Person {
        val rootObject = JSONObject(json)
        return PersonImpl(rootObject.getString(KEY))
    }

    override fun toJson(type: Person): String {
        type as PersonImpl
        val rootObject = JSONObject()
        rootObject.put(KEY, type.name)
        return rootObject.toString()
    }
}
```

### Método fromJson

Esse método recebe um JSONObject como string e retorna uma instância da classe mapeada \(Person\).

### Método toJson

Esse método recebe o tipo de dado mapeado \(Person\) e retorna um JSONObject como uma string.
