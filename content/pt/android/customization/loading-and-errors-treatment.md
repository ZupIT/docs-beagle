---
title: Carregamento e tratamento de erros
weight: 115
description: >-
  Nesta seção, você encontra informações de como customizar o comportamento do
  Beagle para casos de carregamento das telas e tratamento de erros.
---

---

## Introdução

É possível observar o estado atual da tela durante as requisições server-driven em uma BeagleActivity, inclusive estados de erro.

## Exemplo

Ao exibir uma tela  `server-driven` , é possível sobrescrever o método `onServerDrivenContainerStateChange()` para implementar suas tratativas para cada estado da tela.

Abaixo segue um exemplo de uma BeagleActivity com o método `onServerDrivenContainerStateChange()`sobrescrito:


```kotlin
@BeagleComponent
class AppBeagleActivity : BeagleActivity() {

    private val progressBar: ProgressBar by lazy { findViewById<ProgressBar>(R.id.progress_bar) }
    private val mToolbar: Toolbar by lazy { findViewById<Toolbar>(R.id.custom_toolbar) }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_app_beagle)
    }

    override fun getServerDrivenContainerId(): Int = R.id.server_driven_container

    override fun getToolbar(): Toolbar = mToolbar

    override fun onServerDrivenContainerStateChanged(state: ServerDrivenState) {
        when (state) {
            is ServerDrivenState.Started -> {
                progressBar.visibility =  View.VISIBLE
            }
            is ServerDrivenState.Finished -> {
                progressBar.visibility =  View.GONE
            }
            is ServerDrivenState.Error -> {
                Toast.makeText(this, "Error", Toast.LENGTH_LONG).show()
            }
        }
    }
}
```


A partir destes estados, como mostra o exemplo anterior de implementação do loadView, é possível definir quando mostrar ou esconder o Load e definir também outras ações pertinentes aos estados `ServerDrivenState`: `Started`, `Success` , `Finished` e `Error`.  

```kotlin
sealed class ServerDrivenState {
    object Started : ServerDrivenState()
    object Finished : ServerDrivenState()
    object Success : ServerDrivenState()
    open class Error(
        val throwable: Throwable, 
        val retry: BeagleRetry
    ) : ServerDrivenState()
}
```

Veja mais detalhes de cada estado:

* **Started:** Indica que uma busca de componente server-driven foi iniciada.
* **Finished:** Indica que uma busca de componente server-driven foi concluída.
* **Success:** Indica um estado de sucesso ao buscar um componente server-driven.
* **Error:** Indica um estado de erro ao buscar um componente server-driven.
