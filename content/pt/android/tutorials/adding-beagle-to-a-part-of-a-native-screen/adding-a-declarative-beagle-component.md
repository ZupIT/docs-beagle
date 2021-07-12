---
title: Adicionando um componente Beagle Declarativo
weight: 230
description: >-
  Esta seção descreve como adicionar um componente Declarativo do Beagle a uma
  tela nativa
---

---

## Passo 1: Criar a tela nativa

Para criar a tela nativa no Android, crie uma nova Activity. Aqui será usado a `MainActivity` como modelo:


```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        declarative_content.addView(declarativeComponente().toView(this))
    }
}


```


{{% alert color="info" %}}
Nesse momento, a MainActivity.kt não terá modificações. É necessário que o seu arquivo `activity_main.xml` seja modificado primeiro. 
{{% /alert %}}

* Abra o **`activity_main.xml`**
* Por padrão, este arquivo vem configurado com o **`Contraint Layout`** e ele pode ser mantido dessa forma. Para que ele possa ser utilizado pelo Beagle é necessário que se declare um **`Fragment Layout`** nesse  arquivo xml. É nesse fragment que será carregado o componente declarativo.

Veja o exemplo de xml utlizado nesse tutorial, o que finaliza a criação da tela Nativa. 


```markup
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
<FrameLayout
    android:id="@+id/declarative_content"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

</FrameLayout>
</androidx.constraintlayout.widget.ConstraintLayout>
```
## Passo 2: Criar o componente declarativo

Para criar um componente declarativo do Beagle no Android, usaremos um Container \(um componente do Beagle que agrupa outros componentes\). Ele pode ser declarado como uma variável ou como uma função que retorna um container. Nesse exemplo, foi configurado como retorno de uma função:


```text
fun declarativeComponente() = 

    Container( children = 
        listOf(
            Text(
                text = "These buttons are rendered by Beagle"
            ).applyFlex(
                Flex(
                    alignSelf = AlignSelf.CENTER
                )
            ),
            Button(
                "I'm a server-driven button 1", 
                onPress = listOf(
                    Alert(message = "I'm a working button"))
                )
            ),
             Button(
                "I'm a server-driven button too", 
                onPress = listOf(
                    Alert(message = "I'm a working as well"))
                )
            )
        )
    )
```
## Passo 3: Adicionar o componente na tela

Para adicionar esse componente a tela, será necessário chamar a função **`addView`**do Android a partir do **`Fragment Layout view`** que foi declarado no `activity_main.xml` como listado no exemplo abaixo:


```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        declarative_content.addView(declarativeComponente().toView(this))
        
    }
}
```


Como parâmetro do **`addView`** foi listado a função que retorna o componente criado aplicando o método do Beagle **`.toView(this)`**

E pronto. Basta inicializar a sua aplicação e a tela declarativa será renderizada nativamente.
