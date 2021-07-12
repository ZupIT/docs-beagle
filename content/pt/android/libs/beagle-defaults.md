---
title: Beagle Defaults
weight: 17
description: 'Aqui você encontrará a biblioteca **Beagle-Defaults** para ajudá-lo a iniciar um projeto usando o Beagle no Android.
Esta biblioteca conterá apenas algumas classes necessárias
para configurar e adicionar o Beagle em um projeto Android.'
---

---

![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-defaults?color=green&label=Beagle-Defaults)
![Maven Central](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)

### Requisitos
Antes de começar a configurar o Beagle para o seu sistema Android, é importante verificar se você instalou todas as versões atuais dos seguintes programas: ‌

* JDK 8+ language
* Android API level: at least 19 or higher
* Kotlin 1.3+

### Primeiros Passos
<hr>

Este tutorial irá configurar o Beagle desde o início.

Etapa 1: adicione uma configuração ao arquivo Gradle em seu projeto:
- `Adicione mavenCentral ()` no bloco `All projects` em seu arquivo do Projeto Gradle (build.gradle) <br>

```kotlin
allprojects {
    repositories {
        google()
        jcenter()
        mavenCentral()
    }
}
```
Etapa 2: adicione o plugin kotlin kapt e 3 dependências no gerenciador de dependências do seu aplicativo (gradle.gradle) <br>

 ![**Maven Central**](https://img.shields.io/maven-central/v/br.com.zup.beagle/beagle-defaults?color=green&label=Beagle-Defaults)
 ![**Maven Central**](https://img.shields.io/maven-central/v/br.com.zup.beagle/android?label=Beagle)                                                                   

* _Os números de versão listados abaixo podem ter sido atualizados. Verifique as versões reais nas tags acima:_
```kotlin
apply plugin: 'kotlin-kapt'
android {
    kotlinOptions {
        jvmTarget = JavaVersion.VERSION_1_8
    }
}
dependencies {
    implementation "br.com.zup.beagle:android:1.5.1"
    kapt "br.com.zup.beagle:android-processor:1.5.1"
    implementation "br.com.zup.beagle:beagle-defaults:0.0.4"
}
```

Etapa 3: Crie 4 arquivos de configuração para o Beagle.
* Esses arquivos definirão algumas configurações e classes usadas pelo Beagle. <br>
    * se precisar de informações mais detalhadas, verifique nossa documentação

a) Crie um arquivo AppBeagleConfig que herde da classe BeagleConfig:
```
@BeagleComponent
class AppBeagleConfig: BeagleConfig {
    override val environment: Environment = Environment.DEBUG
    override val baseUrl: String = "https://adopt-beagle.continuousplatform.com/scaffold"
    override val isLoggingEnabled: Boolean = true
    override val cache: Cache = Cache(
        enabled = false,
        maxAge = 300,
        size = 15
    )
}
```
b) Crie um arquivo HttpApp conforme listado abaixo. Esta classe herda de uma classe ´HttpClientDefault()´ na biblioteca Beagle-Default:
```
@BeagleComponent
class HttpApp: HttpClientDefault()
```


c) Crie um arquivo CacheApp conforme listado abaixo. Esta classe herda de uma classe ´StoreHandlerDefault()´ na biblioteca Beagle-Default:
```
@BeagleComponent
class CacheApp : StoreHandlerDefault(AppApplication.APPLICATION!!)
```

d) Crie um arquivo LoggerApp conforme listado abaixo. Esta classe herda de uma classe ´BeagleLoggerDefault()´ na biblioteca Beagle-Default:
```
@BeagleComponent
class LoggerApp: BeagleLoggerDefault()
```

Etapa 4: Construa seu projeto: Ao construir este aplicativo, a configuração acima irá configurar o Beagle em seu projeto. <br>
Etapa 5: crie um arquivo AppAplication conforme o exemplo abaixo:
```
class AppApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        APPLICATION = this
        BeagleSetup().init(this)
    }
    companion object {
        var APPLICATION: Application? = null
    }
}
```
Etapa 6: Teste seu aplicativo<br>
Para testá-lo, basta chamar uma tela de nosso exemplo do BFF. <br>
Para fazer isso, você precisa iniciar uma activity usando a configuração abaixo:
```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
       
        val intent = this.newServerDrivenIntent<ServerDrivenActivity>(ScreenRequest("/components"))
        startActivity(intent)
    }
}
```
Uma tela de amostra com todos os componentes do Beagle será exibida na tela do emulador

<hr>

##### Para saber mais sobre o Beagle: Por favor verifique nosso [Beagle](https://github.com/ZupIT/beagle) repositório.
##### Para saber mais sobre o  Beagle-Scaffold: Por favor verifique sua documentação em [Beagle-Scaffold]({{< ref path="/get-started/using-beagle-helpers/android/beagle-scaffold" lang="pt" >}}).