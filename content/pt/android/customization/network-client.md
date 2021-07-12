---
title: Camada de rede
weight: 113
description: >-
  Nesta seção, você encontra informações sobre a camada de rede do Beagle e como
  modificá-la.
---

---

## Introdução

Esta interface define como as requisições de serviços são configuradas, para usá-lo, você precisa criar uma classe que implemente uma interface `HttpClient` .

Aqui, você pode adicionar cabeçalhos às suas requisições, definir os métodos request, body response,  data response, executar criptografia, etc.

```kotlin 
interface HttpClient {

    fun execute(
        request: RequestData,
        onSuccess: (responseData: ResponseData) -> Unit,
        onError: (responseData: ResponseData) -> Unit
    ): RequestCall
}
```

No método execute, você consegue criar as regras de sua camada de rede, fazendo com que o beagle reconheça a sua regra.

| **Atributo** | **Tipo** | **Obrigatório** | **Definição** |
| :--- | :--- | :---: | :---: |
| request | RequestData  | ✓ | RequestData é a classe para fazer configuração e solicitações http. |
| onSuccess | (responseData: ResponseData) -> Unit | ✓ | Higher-Order Functions responsável pelo retorno de sucesso |
| onError | (responseData: ResponseData) -> Unit | ✓ | Higher-Order Functions responsável pelo retorno de erro |

### RequestData 

RequestData é a classe para fazer configuração e solicitações http.

| **Atributo** | **Tipo** | **Obrigatório** | **Definição** |
| :--- | :--- | :---: | :---: |
| uri | URI  | ✓ | URL do servidor. |
| method | HttpMethod | ✓ | Método HTTP |
| headers | Map<String, String> | | Itens do header para a requisição. |
| body | String | | Conteúdo que será entregue com a solicitação. |

#### HttpMethod

É um `ENUM`, cujo os valores são:

| Valor | Definição |
| :--- | :--- |
| GET | O método `GET` solicita a representação de um recurso específico. Requisições utilizando o método `GET` devem retornar apenas dados. |
| POST | O método `POST` é utilizado para submeter uma entidade a um recurso específico, frequentemente causando uma mudança no estado do recurso ou efeitos colaterais no servidor. |
| PUT | O método `PUT` substitui todas as atuais representações do recurso de destino pela carga de dados da requisição. |
| DELETE | O método `DELETE` remove um recurso específico. |
| HEAD | O método `HEAD` solicita uma resposta de forma idêntica ao método `GET`, porém sem conter o corpo da resposta. |
| PATCH | O método `PATCH` é utilizado para aplicar modificações parciais em um recurso. |

### ResponseData

ResponseData é usado para retornar dados feitos pela solicitação.

| **Atributo** | **Tipo** | **Obrigatório** | **Definição** |
| :--- | :--- | :---: | :---: |
| statusCode | Int  | ✓ | http status code da requisição |
| data | ByteArray | ✓ | Response body retornado da requisição |
| headers | Map<String, String> |  | Itens do header para a requisição. |
| statusText | String |  | Mensagem de resposta retornada pelo servidor HTTP remoto. |


## Criando uma camada de rede customizada

Para criar uma camada de rede customizada, siga um exemplo abaixo com as seguintes passos.

### Passo 1: Adicionar as dependências

Localize o arquivo `build.gradle(Module:app) ,` abra-o e role a página para baixo até encontrar o bloco de código **`dependencies { }`**.

1. Copie e cole a linha abaixo dentro das dependências:

   * implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.9'  

2. Aperte **Sync now** para sincronizar o Gradle novamente. 

Essa dependência é necessária, pois a classe que implementa `HttpClient` vai importar algumas configurações dela.

### Passo 2: Criar a object CoroutineDispatchers

Crie um objeto e escolha um nome para ele, por exemplo CoroutineDispatchers 

Esse object é responsável por configurar os CoroutineDispatchers, que irá ditar em qual thread as tarefas serão executadas

```kotlin
import kotlinx.coroutines.CoroutineDispatcher
import kotlinx.coroutines.Dispatchers

internal object CoroutineDispatchers {

    init {
        reset()
    }

    lateinit var IO: CoroutineDispatcher
    lateinit var Main: CoroutineDispatcher
    lateinit var Default: CoroutineDispatcher

    fun reset() {
        IO = Dispatchers.IO
        Main = Dispatchers.Main
        Default = Dispatchers.Default
    }
}
```

### Passo 3: Criar um arquivo HttpURLConnectionExtensions

Crie um arquivo e escolha o nome que você desejar. Nesse exemplo, foi dado o nome de `HttpURLConnectionExtensions`.  

Este arquivo é responsável por conter métodos para retornar a regra * HttpURLConnection * , portanto, usaremos esses métodos na classe HttpClientDefault.

```kotlin
import java.lang.Exception
import java.net.HttpURLConnection

internal fun HttpURLConnection.getSafeResponseCode(): Int? {
    return getMessageFormatted { this.responseCode }
}

internal fun HttpURLConnection.getSafeResponseMessage(): String? {
    return getMessageFormatted { this.responseMessage }
}

internal fun HttpURLConnection.getSafeError(): ByteArray? {
    return getMessageFormatted { this.errorStream.readBytes() }
}

internal typealias GetData<T> = () -> T

internal fun <T> getMessageFormatted(getData: GetData<T>): T? {
    return try {
        getData.invoke()
    } catch (exception: Exception) {
        null
    }
}
```

### Passo 4: Criar a classe HttpClientDefault

Crie uma classe e escolha um nome para ela. Para o exemplo, `HttpClientDefault` foi escolhido.

A classe HTTPClientDefault define como as solicitações de serviços são configuradas. Para usá-la, você precisa criar uma classe que implemente uma interface do tipo `HttpClient`.

Essa configuração é bem extensa, a sugestão é que você copie e cole a classe abaixo e modifique como achar necessário: 


```kotlin
import br.com.zup.beagle.android.annotation.BeagleComponent
import br.com.zup.beagle.android.exception.BeagleApiException
import br.com.zup.beagle.android.networking.HttpClient
import br.com.zup.beagle.android.networking.HttpMethod
import br.com.zup.beagle.android.networking.RequestCall
import br.com.zup.beagle.android.networking.RequestData
import br.com.zup.beagle.android.networking.ResponseData
import kotlinx.coroutines.CoroutineScope
import kotlinx.coroutines.Job
import kotlinx.coroutines.cancel
import kotlinx.coroutines.launch
import java.io.EOFException
import java.net.HttpURLConnection
import java.net.URI

typealias OnSuccess = (responseData: ResponseData) -> Unit
typealias OnError = (responseData: ResponseData) -> Unit

@BeagleComponent
class HttpClientDefault : HttpClient, CoroutineScope {
  private val job = Job()
  override val coroutineContext = job + CoroutineDispatchers.IO
  override fun execute(
    request: RequestData,
    onSuccess: OnSuccess,
    onError: OnError
  ): RequestCall {
    if (getOrDeleteOrHeadHasData(request)) {
      onError(ResponseData(-1, data = byteArrayOf()))
      return createRequestCall()
    }
    launch {
      try {
        val responseData = doHttpRequest(request)
        onSuccess(responseData)
      } catch (ex: BeagleApiException) {
        onError(ex.responseData)
      }
    }
    return createRequestCall()
  }
  private fun getOrDeleteOrHeadHasData(request: RequestData): Boolean {
    val method = request.httpAdditionalData.method
    val body = request.httpAdditionalData.body
    return (method == HttpMethod.GET ||
      method == HttpMethod.DELETE ||
      method == HttpMethod.HEAD) &&
      body != null
  }
  @Throws(BeagleApiException::class)
  private fun doHttpRequest(
    request: RequestData
  ): ResponseData {
    val urlConnection: HttpURLConnection
    try {
      val uri = URI(request.url)
      urlConnection = uri.toURL().openConnection() as HttpURLConnection
    } catch (e: Exception) {
      throw BeagleApiException(ResponseData(-1, data = byteArrayOf()), request)
    }
    request.httpAdditionalData.headers?.forEach {
      urlConnection.setRequestProperty(it.key, it.value)
    }
    addRequestMethod(urlConnection, method)
    request.httpAdditionalData.body?.run {
      setRequestBody(urlConnection, request)
    }
    try {
      return createResponseData(urlConnection)
    } catch (e: Exception) {
      throw tryFormatException(urlConnection, request)
    } finally {
      urlConnection.disconnect()
    }
  }
  private fun tryFormatException(
    urlConnection: HttpURLConnection,
    request: RequestData
  ): BeagleApiException {
    val response = urlConnection.getSafeError() ?: byteArrayOf()
    val statusCode = urlConnection.getSafeResponseCode()
    val statusText = urlConnection.getSafeResponseMessage()
    val responseData = ResponseData(
      statusCode = statusCode,
      data = response,
      statusText = statusText,
    )
    return BeagleApiException(responseData, request)
  }
  private fun addRequestMethod(urlConnection: HttpURLConnection, method: HttpMethod) {
    val methodValue = method.toString()
    if (method == HttpMethod.PATCH || method == HttpMethod.HEAD) {
      urlConnection.setRequestProperty("X-HTTP-Method-Override", methodValue)
      urlConnection.requestMethod = "POST"
    } else {
      urlConnection.requestMethod = methodValue
    }
  }
  private fun setRequestBody(urlConnection: HttpURLConnection, request: RequestData) {
    try {
      urlConnection.doOutput = true
      urlConnection.outputStream.write(request.httpAdditionalData.body?.toByteArray())
      urlConnection.outputStream.flush()
    } catch (e: Exception) {
      throw BeagleApiException(ResponseData(-1, data = byteArrayOf()), request)
    }
  }
  private fun createResponseData(urlConnection: HttpURLConnection): ResponseData {
    return ResponseData(
      statusCode = urlConnection.responseCode,
      statusText = urlConnection.responseMessage,
      headers = urlConnection.headerFields.filter { it.key != null }.map {
        val headerValue = it.value.toString()
          .replace("[", "")
          .replace("]", "")
        it.key to headerValue
      }.toMap(),
      data = try {
        urlConnection.inputStream.readBytes()
      } catch (e: EOFException) {
        byteArrayOf()
      }
    )
  }
  private fun createRequestCall() = object : RequestCall {
    override fun cancel() {
      this@HttpClientDefault.cancel()
    }
  }
}
```

### Passo 5: Criar a classe HttpClientFactoryDefault

Crie uma classe e escolha um nome para ela, por exemplo `HttpClientFactoryDefault`.

A classe HttpClientFactoryDefault é responsável por criar a instância de HttpClient para o Beagle. Essa classe é particularmente útil quando você precisa configurar ou passar parâmetros na instanciação do seu HttpClient. Para usá-la, você precisa criar uma classe que implementa a interface `HttpClientFactory`.

Como exemplo, você pode copiar e colar a classe abaixo e modificar como achar necessário:

```kotlin
import br.com.zup.beagle.android.annotation.BeagleComponent
import br.com.zup.beagle.android.networking.HttpClient
import br.com.zup.beagle.android.networking.HttpClientFactory
@BeagleComponent
class AppHttpClientFactory: HttpClientFactory {
    override fun create(): HttpClient {
        return HttpClientDefault()
    }
}```
