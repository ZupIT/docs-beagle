---
title: RootView
weight: 121
description: >-
  Interface RootView detém a referência de activity ou fragment.
---

---

## RootView

A interface RootView detém a referência de uma activity ou fragment. Atravéz dos métodos da interface, você tem referência de contexto, ciclo de vida, ViewModelStore e id da view pai.

RootView está presente como atributo nas interfaces ViewConvertable e Action.

```kotlin
interface RootView {

    fun getContext(): Context

    fun getLifecycleOwner(): LifecycleOwner

    fun getViewModelStoreOwner(): ViewModelStoreOwner

    fun getParentId(): Int
}
```

### getContext()

Retorna o contexto

```kotlin
fun getContext(): Context
```

### getLifecycleOwner()

Retorna um lifecycleOwner.

```kotlin
fun getLifecycleOwner(): LifecycleOwner
```

### getViewModelStoreOwner()

Retorna um ViewModelStore para activity e fragment.

```kotlin
fun getViewModelStoreOwner(): ViewModelStoreOwner
```

### getParentId()

Retorna o id da View pai que encapsula todo o conteúdo renderizado.

```kotlin
fun getParentId(): Int
```

## ActivityRootView

A classe ActivityRootView é responsavel por guardar a referência da Activity da qual um metodo [**loadView**]({{< ref path="/tutorials/adding-beagle-to-a-part-of-a-native-screen/adding-a-beagle-server-driven-component" lang="pt" >}}) é chamado.

```kotlin
class ActivityRootView(
    val activity: AppCompatActivity,
    private val parentId: Int
) : RootView {

    override fun getContext(): Context = activity

    override fun getLifecycleOwner(): LifecycleOwner = activity

    override fun getViewModelStoreOwner(): ViewModelStoreOwner = activity

    override fun getParentId(): Int = parentId
}
```

| **Atributo** | **Tipo**          | **Obrigatório** | **Definição**                      |
| :----------- | :---------------- | :-------------: | :--------------------------------- |
| activity     | AppCompatActivity |        ✓        | Recebe a instância de uma activity |
| parentId     | Int               |        ✓        | Id view pai.                       |

## FragmentRootView

A classe FragmentRootView é responsavel por guardar a referência do Fragment do qual um metodo [**loadView**]({{< ref path="/tutorials/adding-beagle-to-a-part-of-a-native-screen/adding-a-beagle-server-driven-component" lang="pt" >}}) é chamado.

```kotlin
class FragmentRootView(
    val fragment: Fragment,
    private val parentId: Int
) : RootView {

    override fun getContext(): Context = fragment.requireContext()

    override fun getLifecycleOwner(): LifecycleOwner = fragment.viewLifecycleOwner

    override fun getViewModelStoreOwner(): ViewModelStoreOwner = fragment

    override fun getParentId(): Int = parentId
}
```

| **Atributo** | **Tipo** | **Obrigatório** | **Definição**                     |
| :----------- | :------- | :-------------: | :-------------------------------- |
| fragment     | Fragment |        ✓        | Recebe a instância de um fragment |
| parentId     | Int      |        ✓        | Id view pai.                      |
