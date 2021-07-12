---
title: Loading and errors treatment
weight: 128
description: >-
  You will find here information on how to customize Beagle's behaviour to each
  loading screens and errors treatments.
---

---

## Introduction

It is possible to watch the screen's current state during server-driven requests in a BeagleActivity, including error states. 

## Example

To display a server-driven screen, it is possible to overwrite the `onServerDrivenContainerStateChange()` method to implement its handles to each screen state.

See below a BeagleActivity example with the overwritten `onServerDrivenContainerStateChange()` method:


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


On the `loadView`, you can also see that these states shows the loadView implementation, it is possible to define when to show or to hide Load. And you can also define other action to the states `ServerDrivenState`, that are`Started`, `Success` , `Finished` and `Error`

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

See more details about each state:

* **Started:** Indicates that a server-driven component fetch has begun.
* **Finished:** Indicates that a server-driven component fetch has finished.
* **Success:** Indicates a success state while fetching a server-driven component.
* **Error:** Indicates an error state while fetching a server-driven component.
