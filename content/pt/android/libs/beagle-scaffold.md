---
title: Beagle Scaffold
weight: 16
description: 'Aqui você encontrará a biblioteca **Beagle-Scaffold** para ajudá-lo a iniciar um projeto usando o Beagle no Android.
Esta biblioteca conterá quase todas as configurações necessárias para adicionar o Beagle a um projeto Android.'
---

---


![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-scaffold?color=green&label=Beagle-Scaffold)
![**Maven Central**](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)

### Requisitos
Antes de começar a configurar o Beagle para o seu sistema Android, é importante verificar se você instalou todas as versões atuais dos seguintes programas:

* JDK 8+ language
* Android API level: at least 19 or higher
* Kotlin 1.3+

### Primeiros Passos
<hr>

Este tutorial irá configurar o Beagle desde o início.

#### Etapa 1: adicione uma configuração ao arquivo Gradle em seu projeto:
- `Adicione mavenCentral()` no bloco `All projects` em seu arquivo do Projeto Gradle (build.gradle) <br>

```kotlin
allprojects {
    repositories {
        google()
        jcenter()
        mavenCentral()
    }
}
```
#### Etapa 2: adicione o plugin kotlin kapt e 3 dependências no gerenciador de dependências do seu aplicativo (gradle.gradle) <br>

 ![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-scaffold?color=green&label=Beagle-Scaffold)
 ![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)                                                                   

* Adicione o bloco de código packagingOptions no arquivo build.gradle, como no exemplo abaixo.

* _Os números de versão listados abaixo podem ter sido atualizados. Verifique as versões reais nas tags acima:_
```kotlin
plugins {
    id 'com.android.application'
    id 'kotlin-android'
    id 'kotlin-android-extensions'
    id 'kotlin-kapt'
}

android {
    kotlinOptions {
        jvmTarget = JavaVersion.VERSION_1_8
    }
}

dependencies {
    implementation "br.com.zup.beagle:android:1.7.0"
    kapt "br.com.zup.beagle:android-processor:1.7.0"
    implementation "br.com.zup.beagle:beagle-scaffold:1.7.0"
}
```

#### Etapa 3: Crie um AppBeagleConfigFile
* Este arquivo obterá todos os seus atributos de configuração da biblioteca beagle-scaffold. <br>
Se necessitar de uma informação mais detalhada consulte a nossa documentação

```
@BeagleComponent
class AppBeagleConfig: BeagleConfigScaffold()
```
#### Etapa 4: Construa seu projeto: 
Ao construir este aplicativo, a configuração acima irá criar o setup do Beagle na sua aplicação<br>
* Clique na aba **Build > Make Project**

#### Etapa 5: crie um arquivo AppAplication conforme o exemplo abaixo:
```
class AppApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        BeagleScaffold(BeagleSetup()).init(this)
    }
}
```

#### Etapa 6: Atualizando o Android Manifest

Neste ultimo passo você irá adicionar duas configurações ao android manifest: A permissão para internet no android manifest e o nome da sua`application`. 

* Para habilitar a permissão para internet, adicione a linha do código abaixo: 

```markup
<uses-permission android:name="android.permission.INTERNET" />
```

* Adicione o nome de sua aplicação ao android manifest:

```markup
<application
        android:name=".AppApplication"

        ...
```

* O seu codigo deve ficar como no exemplo abaixo:

```markup
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.beagleexamples">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:name=".AppApplication"

        ...
```

#### Etapa 7: Teste seu aplicativo <br>
Para testá-lo, basta chamar uma tela de nosso exemplo do BFF. <br>
Para fazer isso, você precisa iniciar uma activity usando o BeagleIntent <br>
da biblioteca Beagle-Scaffold exatamente como mostrado abaixo:
```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        //Server-driven Screen
        startActivity(BeagleIntent(this).toSample())
    }
}
```
Uma tela de amostra com todos os componentes do Beagle será exibida na tela do emulador

<hr>

##### Para saber mais sobre o Beagle: Por favor verifique nosso [Beagle](https://github.com/ZupIT/beagle) repositório.
##### Para saber mais sobre o Beagle-Defaults: Por favor verifique sua documentação em [Beagle Defaults]({{< ref path="/get-started/using-beagle-helpers/android/beagle-defaults" lang="pt" >}}).
