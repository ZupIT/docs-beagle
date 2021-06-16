---
title: Instalando o Beagle
weight: 120
description: Nessa seção, você irá aprender a como criar um projeto android e configurá-lo para utilizar o Beagle
---
**Tópicos abordados:**
- Criar um projeto Android do zero
- Adicionar o Beagle no seu projeto (pode ser em projetos já existentes)
- Configurar o Beagle
- Customizações possíveis

**Requisitos:**
 - Android Studio
 - minSdkVersion: 19+
 - JDK 8+ language
 - Kotlin 1.3+

## **Criando um projeto Android**

Para este exemplo prático, utilizaremos o Android Studio IDE. Caso você ainda não o tenha instalado, basta acessar no [**site oficial do Android** ](https://developer.android.com/studio?hl=us-en)e seguir as instruções.

Depois de ter instalado o programa, siga os passos abaixo:

**Passo 1:** Abra o Android Studio e clique em **Start a new Android Studio project**_._

![](https://lh4.googleusercontent.com/bAhbvEZUN_pBXavMOqCvkt2Z4NlYsxXXtmeGRKEUnyGfuIm7c-mskMhmmiMbSaCw_xonW8vceVI2C27q08-k5tV8EDD5ymvoaPwDDFGe_fN3bmoqCQEoAAKASKXOTiI3KUPI1GQ1)

**Passo 2:** Selecione a opção **Empty Activity** e clique em **next**.

![](https://lh5.googleusercontent.com/nT0zkr0W_Ark0ZZDR2eWtCtfnjC_Fm98VSwU3DgBQzcgh_DoqkYNvhINztNL460p0U2hnygJ5K_DhrZMZis0mqHD69QJgKimruICs4MnBnPO9m-m_2T6E1nWIXiOfaIe0iiCjIN3)

**Passo 3️:** Nesta página, devemos listar algumas informações importantes:

- Informe o nome do seu projeto. Neste exemplo, chamaremos de `BeagleApp`.
- Selecione qual linguagem utilizará. Para o Beagle, devemos utilizar o`Kotlin`.
- Selecione o **SDK mínimo 19 ou um valor superior**, já que qualquer SDK menor que este não será compatível.
- Defina o **package** e a **Save location** de acordo com sua preferência.
- Clique em **Next**.

![](https://lh3.googleusercontent.com/m5jnbs4K5AdwQbA7YVn7fSgtVzw5S68yCbGTj_7-CYa9CGvMR-qFO5EQ4SaNehXYRmI4WOOnqA6JQouzW2QC0YMTpBq7kJSbi54yl0Q2emL_y2FizY3LyloLjuh_uDyf8WyVbodP)

**Passo 4️:** Feitas as configurações anteriores, o Android levará um tempo pra construir o projeto porque estará sincronizando todas as dependências inicias para inicializar o projeto

Quando a inicialização for concluída, você verá esta página:

![](/shared/mainactivity.png)

## **Adicionando o Beagle no seu projeto**

Para adicionar o Beagle em seu projeto Android e poder aproveitar desse framework, siga os passos abaixo:

**Passo 1:** Configurar as dependências

Você precisa ajustar as dependências do seu repositório para receber o Beagle. Para isso, utilize as configuração abaixo para fazer o **download da biblioteca**.

```kotlin
allprojects {
    repositories {
        google()
        mavenCentral()
    }
}
```

{{% alert color="warning" %}}
A configuração acima deve ser adicionada em `allprojects{}`
{{% /alert %}}

**Passo 2:** Incluir os plugins

Uma vez feita a primeira configuração, agora você precisa incluir o plugin do `kapt` e do Beagle como dependências dentro do seu gerenciador de dependências, como no exemplo abaixo.

A versão atual do Beagle é: [![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/android)](https://mvnrepository.com/artifact/br.com.zup.beagle/android)


```kotlin
plugins {
	id 'kotlin-kapt'
}

android {
    kotlinOptions {
        jvmTarget = JavaVersion.VERSION_1_8
    }
}

ext.beagle_version = "${beagle_version}"

dependencies {
    implementation "br.com.zup.beagle:android:$beagle_version"
    kapt "br.com.zup.beagle:android-processor:$beagle_version"
}
```

Insira a versão de release do Beagle no lugar de `${beagle.version}`, ou seja, coloque a versão do Beagle destacada em azul da badge acima, mas sem o **caracter v** que antecede os números de versão.

Por exemplo: undefined-`ext.beagle.version = "1.8.0"`

{{% alert color="warning" %}}
Lembre de sempre verificar se você está usando a versão mais recente do Beagle. Para saber disso, basta passar o mouse por cima do número da versão. Depois disso, sincronize com sua máquina.
{{% /alert %}}
{{%alert color="warning"%}}
Lembre de sempre usar a mesma versão do Beagle utilizada no seu BFF para evitar possíveis problemas
{{%/alert%}}


## **Configurando o Beagle**

Para configurar o Beagle após ter sido adicionado em seu projeto, siga os passos abaixo:

**Passo 1:** Criar uma classe BeagleConfig

Feita a atualização, você precisa criar uma classe `BeagleConfig` e configurar seus atributos, como mostra o exemplo abaixo:

```kotlin
@BeagleComponent
class AppBeagleConfig : BeagleConfig {
    override val environment: Environment get() = Environment.DEBUG
    override val baseUrl: String get() = "https://myapp.server.com"
    override val isLoggingEnabled: Boolean = true
    override val cache: Cache = Cache(
        enabled = false,
        maxAge = 300,
        size = 15
    )
}
```

| Atributos        | Tipo        | Definição                                                                                                                                                                                                               |
| :--------------- | :---------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| environment      | Environment | Atributo responsável por informar ao Beagle qual o estado de build atual da aplicação.                                                                                                                                  |
| baseUrl          | String      | Informa a url base usada no Beagle na aplicação. Se estiver usando emulador, consulte essa [**página para definir esse atributo**](https://developer.android.com/studio/run/emulator-networking.html#networkaddresses). |
| isLoggingEnabled | Boolean     | Atributo que habilita ou desabilita todos os logs que o Beagle gera.                                                                                                                                                    |
| cache            | Cache       | Objeto responsável por gerenciar o cache das requisições do Beagle.                                                                                                                                                     |

{{% alert color="warning" %}}
Faça a configuração das classes com bastante atenção, pois se você anotá-las com `BeagleComponent`, o Beagle espera que elas tenham construtores vazios.
{{% /alert %}}

**Passo 2:** Criar o BeagleSetup

Após ter criado a classe mostrada no passo anterior e anotado com o @BeagleComponent, basta você buildar seu projeto (para isso, aperte CTRL + F9) que o Beagle irá criar automaticamente uma classe de `BeagleSetup` class, como mostra a figura abaixo:

![BeagleSetup file](/shared/beaglesetup.png)

**Passo 3:** Criar a classe Application

Neste momento, você deve criar uma `classe Kotlin` que estenda a classe `Application` que, para este exemplo, nomeamos como `AppApplication`.

Esta classe deve chamar o `BeagleSetup().init(this)` no método `onCreate`, conforme listado abaixo:

```kotlin
class AppApplication: Application() {

    override fun onCreate() {
        super.onCreate()

        BeagleSetup().init(this)
    }
}
```

**Passo 4:** Atualizar seu Android Manifest.xml

Por fim, você deve atualizar novamente o seu `AndroidManifest.xml` e definir a `AppApplication` que foi criada como o arquivo de inicialização da aplicação, como no exemplo abaixo:

```markup
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.beagleexamples">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:name=".AppApplication"

        ..
```

{{% alert color="warning" %}}
Para fazer requisições server-driven a um backend é necessário configurar uma camada de rede. Para saber como, clique aqui!
{{% /alert%}}

## **Outras Customizações**
 * Ações - você pode criar ações customizadas para serem executadas pelos widgets conforme interações do usuário
 * Animações de navegação - você pode customizar as animações de navegação das telas do Beagle
 * Beagle Activity - você pode customizar a activity que será exibida quando uma tela server-driven for chamada
 * Camada de Rede - você pode customizar a camada de rede para configurar a forma que o Beagle irá fazer as requisições
 * Cache - você pode customizar o cache para salvar as telas server-driven
 * Carregamento e tratamento de erros - você pode customizar o tratamento de erro e o loading das telas server-driven
 * Proguard - 
 * Deep Link handler -
 * Image Downloader - você pode customizar a forma como as imagens serão baixadas para serem exibidas pelo Beagle
 * Design System - você pode customizar seu design system para construir telas mais bonitas
 * Serializador e Desserializador - 
 * Log - 
 * Validador -
 * Widget -
 * Operações -