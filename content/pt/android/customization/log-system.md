---
title: Sistema de Log
weight: 125
description: >-
  Nesta seção, você encontra a descrição da classe BeagleLogger e detalhes de
  configuração e customização.
---

---

## Introdução

O sistema de logs do Beagle permite que você investigue erros durante o desenvolvimento e problemas com o aplicativo em produção.

As mensagens de **log** fornecidas pelo Beagle Android podem ser acessadas pelo: 

* Console do Android Studio
* Console da Aplicação

O sistema de log é dividido em 3 categorias: 

```kotlin 
interface BeagleLogger {

    fun warning(message: String)

    fun error(message: String)

    fun error(message: String, throwable: Throwable)

    fun info(message: String)
}
```

1. **Info:** relacionada à camada Rede, informações e respostas da comunicação com o servidor. 
2. **Warning:** mensagens informativas de erros relacionados a componentes do Beagle.
3. **Error**: exibe mensagens de erros relacionadas a `parse`, `http` e outras exceções. 

É possível **desabilitar** **as mensagens** de logs disparadas. Dessa forma, o Beagle não irá mais chamar a API de Log, seja ela a padrão ou customizada. Você precisa passar o atributo `isLoggingEnabled` do `AppBeagleConfig` para `false:`


```kotlin
@BeagleComponent
class AppBeagleConfig : BeagleConfig {

    override val isLoggingEnabled: Boolean = false

}
```


## Customização

Você precisa criar uma classe que implemente a interface BeagleLogger. Esse protocolo deverá ser definido para facilitar o monitoramento dos erros gerados no contexto server-driven de sua aplicação. Você pode implementar os métodos de acordo com a sua necessidade.

Para deixar o ambiente do Beagle aberto a modificações, a API de Logs padrão pode ser substituída por qualquer outra.

A customização é configurada com a criação de uma classe anotada com `@BeagleComponent` que implemente a **Interface** `BeagleLogger`. 

Essa interface precisa da implementação dos métodos de logs, você pode ver como funciona no exemplo abaixo:

```kotlin
import android.util.Log
import br.com.zup.beagle.android.annotation.BeagleComponent
import br.com.zup.beagle.android.logger.BeagleLogger

private const val BEAGLE_TAG = "BeagleSDK"

@BeagleComponent
class BeagleLoggerDefault : BeagleLogger {

    override fun warning(message: String) {
        Log.w(BEAGLE_TAG, message)
    }

    override fun error(message: String) {
        Log.e(BEAGLE_TAG, message)
    }

    override fun error(message: String, throwable: Throwable) {
        Log.e(BEAGLE_TAG, message, throwable)
    }

    override fun info(message: String) {
        Log.i(BEAGLE_TAG, message)
    }

}
```
