---
title: Usando widget Id
weight: 241
description: Como set e obter um id em um widget
---

---

Para testar sua aplicação, você precisa de um Widget ID. Para fazer isso, Beagle oferece alguns métodos **set** e **get.**

Nessa seção, você verá dois específicos:

1. **Set Id**
2. **Get Id**

## Set Id

O método Set Id é usado quando você precisa setar um Id no [**widget**]({{< ref path="/api/widget" lang="pt" >}}) fornecido para o [**BFF**]({{< ref path="/key-concepts#backend-for-frontend" lang="pt" >}}). Para fazer isso, você precisa chamar esse método no widget que você quer o Id setado durante o momento que você está criando o seu widget.

Veja como funciona abaixo:

```kotlin
Text(text = "Text with id").setId("myTextId")
```

## Get Id

No Android para obter um Widget Id você precisa chamar a `String.toAndroidId()` e esse método retornará um Id para você usar onde precisar.

Veja como funciona, abaixo:

```kotlin
"myTextId".toAndroidId()
```

O valor do Widget Id será personalizado, se o backend setar o Id \(como no exemplo, "myTextId"\) ou ele será um valor default \('data-beagle-id'\).
