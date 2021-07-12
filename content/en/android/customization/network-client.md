---
title: Network Client
weight: 126
description: >-
  You will find here, information about Beagle's network client and how to
  modify it
---

---

## Introduction 

This interface defines how the services requests are configured, to use it, you need to create a class that implements a `HttpClient` interface.

You can add headers to your requests, define method request, body response, data response, run cryptography, etc. 

```kotlin 
interface HttpClient {

    fun execute(
        request: RequestData,
        onSuccess: (responseData: ResponseData) -> Unit,
        onError: (responseData: ResponseData) -> Unit
    ): RequestCall
}
```

In the **execute**  method, you can create the rules for your network layer, causing Beagle to recognize its rule.

| **Attribute** | **Type** | **Required** | **Definition** |
| :--- | :--- | :---: | :---: |
| request | RequestData  | v | RequestData is the class for configuring http requests. |
| onSuccess | (responseData: ResponseData) -> Unit | ✓ | Higher-Order Functions responsible for the return of success |
| onError | (responseData: ResponseData) -> Unit | ✓ | Higher-Order Functions responsible for error return |

### RequestData 

RequestData is the class for configuring http requests.

| **Attribute** | **Type** | **Required** | **Definition** |
| :--- | :--- | :---: | :---: |
| uri | URI  | ✓ | Defines the endpoint that returns the screen or component you wish to display. |
| method | HttpMethod | ✓ | It is an ENUM class that defines which HTTP operation you wish to do. It works as a HTTP REQUEST METHOD and it is set as GET by default. |
| headers | Map<String, String> | | It is used when you need to send data via an HTTP header.  |
| body | String | | It is set default as null and it just needs to be implemented when you need to send a HTTP messages asbody data. |

#### HttpMethod

It is an `ENUM` and the values are:

| Value | Definition |
| :--- | :--- |
| GET | The `GET` method  requests a representation of a specific resource. Requests using the method `GET` must return only data. |
| POST | The `POST` method is used to submit an entity to a specific resource, frenquetly causing a change in the resource state or colateral effects on the server.  |
| PUT | The  `PUT` method replaces all the current representation of the target resources with the data of the request.  |
| DELETE | The `DELETE` method removes a specific resource.  |
| HEAD | The `HEAD` method  request an answer the same way the `GET` method does, however without a response body.  |
| PATCH | The `PATCH` method is used to apply partial modifications in a resource. |

### ResponseData

ResponseData is used to return data made by the request.

| **Attribute** | **Type** | **Required** | **Definition** |
| :--- | :--- | :---: | :---: |
| statusCode | Int  | ✓ | Returns the response code returned by the remote HTTP server. |
| data | ByteArray | ✓ | Response body returned from request. |
| headers | Map<String, String> | | It is used when you need to send data via an HTTP header. |
| statusText | String | | Returns the response message returned by the remote HTTP server. |

## Create a custom network client

To create this class, follow the next steps:

### Step 1: Add the dependencies 

Locate the file `build.gradle(Module:app) ,` open it and scroll the page until you find the code block  **`dependencies { }`**.

1. Copy and paste the line below inside the dependencies:

   * implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.9'


2. Press **Sync now** to synchronize the Gradle again. 

This dependency is necessary, because the class that implements `HttpClient` will import some of its configuration.

### Step 2: Create a CoroutineDispatchers object

Create an object and choose a name for it, for example, `CoroutineDispatchers`.

This object is responsible for configuring **CoroutineDispatchers** and will dictate which thread the tasks will run.

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

### Step 3: Create a HttpURLConnectionExtensions file

Create a file and choose a name for it, for example,`HttpURLConnectionExtensions`. 
This file is responsible for containing methods returning the **HttpURLConnection** rule, so you will use these methods in the HttpClientDefault class.

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

### Step 4: Create a HttpClientDefault class

Create a class and choose a name for it, for example `HttpClientDefault`.

HttpClientDefault class defines how the services requests are configured. To use it, you need to create a class that implements the `HttpClient` interface.

This configuration is long, so copy and paste the class below. You may modify it later.

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

### Step 5: Create a HttpClientFactoryDefault class

Create a class and choose a name for it, for example `HttpClientFactoryDefault`.

HttpClientFactoryDefault class is responsible for creating the HttpClient instance for Beagle. This class is particularly useful when you need to configure or pass parameters in your HttpClient instantiation. To use it, you need to create a class that implements the `HttpClientFactory` interface.

As an example, you can copy and paste the class below. You may modify it later.

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
