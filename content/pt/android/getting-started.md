---
title: Primeiros Passos
weight: 4
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

**Passo 1: Abra o Android Studio e clique em Start a new Android Studio project**

![](/shared/newandroidproject.png)

**Passo 2:** Selecione a opção **Empty Activity** e clique em **next**.

{{< figure src="/shared/androidprojecttemplate.png">}}

**Passo 3️: Nesta página, você deve adicionar algumas informações importantes:**

- Informe o nome do seu projeto. Neste exemplo, chamaremos de `BeagleApp`.
- Selecione qual linguagem utilizará. Para o Beagle, devemos utilizar o`Kotlin`.
- Selecione o **SDK mínimo 19 ou um valor superior**, já que qualquer SDK menor que este não será compatível.
- Defina o **package** e a **Save location** de acordo com sua preferência.
- Clique em **Next**.

{{< figure src="/shared/configureandroidproject.png">}}

**Passo 4️: Depois de terminar as configurações, o Android levará um tempo pra construir o projeto porque ele precisa sincronizar todas as dependências iniciais para inicializar o projeto.**

Quando a inicialização for concluída, você verá a página abaixo:

{{< figure src="/shared/mainactivity.png">}}

## **Adicionando o Beagle no seu projeto**

Para adicionar o Beagle em seu projeto Android e utilizar esse framework, siga os passos abaixo:

**Passo 1: Configurar as dependências**

Essa configuração é feita no `build.gradle`

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

**Passo 2: Incluir os plugins**

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
Use a mesma versão do Beagle utilizada no seu BFF para evitar possíveis problemas.
{{%/alert%}}


## **Configurando o Beagle**

Para configurar o Beagle após ter sido adicionado em seu projeto, siga os passos abaixo:

**Passo 1: Criar uma classe BeagleConfig**

Depois de finalizar a atualização, você precisa criar uma classe `BeagleConfig` e configurar seus atributos, veja o exemplo abaixo:

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
Cuidado ao fazer a configuração das classes, porque se você anotá-las com `BeagleComponent`, o Beagle espera que elas tenham construtores vazios.
{{% /alert %}}

**Passo 2: Criar o BeagleSetup**

Após ter criado a classe mostrada no passo anterior e anotado com o @BeagleComponent, basta você buildar seu projeto (para isso, acesse o menu Build > Make Project) que o Beagle irá criar automaticamente uma classe de `BeagleSetup` class, como mostra a figura abaixo:

{{< figure src="/shared/beaglesetup.png">}}

**Passo 3: Criar a classe Application**

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

**Passo 4: Atualizar seu Android Manifest.xml**

Atualize novamente o seu `AndroidManifest.xml` e defina a `AppApplication` que foi criada como o arquivo de inicialização da aplicação, veja o exemplo abaixo:

```markup
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.beagleexamples">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:name=".AppApplication"
        android:usesCleartextTraffic="true"
        ..
```

{{%alert color="warning" %}}
  A propriedade `android:usesCleartextTraffic` do application é ativado para permitir fazer requisições locais, pois ele permite realizar tráfego de rede em texto simples, como HTTP
{{% /alert%}}
{{% alert color="warning" %}}
Para fazer requisições server-driven a um backend é necessário configurar uma camada de rede. Para saber como, clique aqui!
{{% /alert%}}

## **Outras Customizações**
 * **Ações:** Você pode criar ações customizadas para serem executadas pelos widgets conforme interações do usuário.
 * **Animações de navegação:** Você pode customizar as animações de navegação das telas do Beagle.
 * **Beagle Activity:** Você pode customizar a activity que será exibida quando uma tela server-driven for chamada.
 * **Camada de Rede:** Você pode customizar a camada de rede para configurar a forma que o Beagle irá fazer as requisições.
 * **Cache:** Você pode customizar o cache para salvar as telas server-driven.
 * **Carregamento e tratamento de erros:** Você pode customizar o tratamento de erro e o loading das telas server-driven.
 * **Proguard:** Você pode ofuscar e otimizar seu código usando Proguard, todas as configurações já estão definidas internamente. Caso queira criar componentes customizados ou alterar alguma regra para o Proguard, o Beagle oferece suporte para isso.
 * **Deep Link handler:** Você pode configurar o Deep Link Handler para ter navegação de uma tela server-driven para uma tela nativa.
 * **Image Downloader:** Você pode customizar a forma como as imagens serão baixadas para serem exibidas pelo Beagle.
 * **Design System:** Você pode customizar seu design system para construir telas mais bonitas.
 * **Serializador e Desserializador:** Você pode customizar a forma como o Beagle serializa e desserializa.
 * **Log:** Você pode customizar a maneira como os logs gerados pelo Beagle serão exibidos.
 * **Widget:** Você pode criar componentes específicos para seu projeto e utilizá-los na criação de telas server-driven.
 * **Operações:** Você pode criar suas operações para manipular o Context de uma forma mais simples através do seu backend.
 * **Analytics:** Você pode configurar um analytics para gerar reportes de telas e ações. 
