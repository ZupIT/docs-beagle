---
title: BeagleImageDownloader
weight: 121
description: >-
  Nesta seção, você encontra a descrição da classe BeagleImageDownloader e detalhes dos
  métodos que a implementa.
---

---

## Introdução

Você pode registrar a forma como imagens remotas serão baixadas e preenchidas nas **ImageView** fornecendo uma classe que implementa essa lógica, que foi adicionado ao Beagle 1.3.0

Com o **BeagleImageDownloader** sendo criado no frontend sua aplicação Android, o beagle saberá qual lógica usar para baixar e configurar os componentes **Image** com **ImagePath.Remote**.

### Passo 1: Adicionar as dependências

Localize o arquivo `build.gradle(Module:app),` abra-o e role a página para baixo até encontrar o bloco de código **`dependencies { }`**.

1. Copie e cole a linha abaixo dentro das dependências:

   * _implementation 'androidx.core:core-ktx:1.2.0'_
   * _implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.9'_
   * _implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.2.0'_

   \_\_

2. Clique em **Sync now** para sincronizar o Gradle novamente. 
3. Essa dependência é necessária, pois a classe que implementa `BeagleImageDownloader` vai importar algumas configurações dela.

### Passo 2: Criar a classe

```kotlin

import android.graphics.Bitmap
import android.graphics.BitmapFactory
import android.graphics.drawable.BitmapDrawable
import android.util.Log
import android.util.LruCache
import android.widget.ImageView
import androidx.lifecycle.lifecycleScope
import br.com.zup.beagle.android.annotation.BeagleComponent
import br.com.zup.beagle.android.imagedownloader.BeagleImageDownloader
import br.com.zup.beagle.android.widget.RootView
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.launch
import kotlinx.coroutines.withContext
import java.io.InputStream
import java.net.URL

const val BASE_URL = "http://base.url"

@BeagleComponent
class CustomImageDownloader : BeagleImageDownloader {

    private val imageDownloader: ImageDownloader = ImageDownloader()

    override fun download(url: String, imageView: ImageView, rootView: RootView) {
        imageView.post {
            rootView.getLifecycleOwner().lifecycleScope.launch(Dispatchers.IO) {
                val bitmap = try {
                    imageDownloader.getRemoteImage(BASE_URL + url, imageView.width, imageView.height)
                } catch (e: Exception) {
                    Log.e("LOG_TAG", e.message ?: "Error when try to download Image")
                    null
                }

                bitmap?.let {
                    setImage(imageView, bitmap)
                }
            }
        }
    }

    private suspend fun setImage(view: ImageView, bitmap: Bitmap?) {
        withContext(Dispatchers.Main) {
            view.context?.let {
                view.setImageDrawable(BitmapDrawable(it.resources, bitmap))
            }
        }
    }
}


internal class ImageDownloader {

    suspend fun getRemoteImage(url: String, contentWidth: Int, contentHeight: Int) : Bitmap? {
        val cacheId = LruImageCache.generateBitmapId(url, contentWidth, contentHeight)

        return withContext(Dispatchers.IO) {
            val bitmapCached = LruImageCache.get(cacheId)

            bitmapCached
                ?: url.let {
                    downloadBitmap(it, contentWidth, contentHeight).apply {
                        LruImageCache.put(cacheId, this)
                    }
                }
        }
    }

    private fun downloadBitmap(url: String?, contentWidth: Int, contentHeight: Int) : Bitmap {
        val inputStream: InputStream = URL(url).openStream()
        return BitmapFactory.decodeStream(inputStream)
    }

}

internal object LruImageCache {

    private val cache: LruCache<String, Bitmap> by lazy {
        LruCache<String, Bitmap>(anEighthOfMemory())
    }

    fun put(key: String, bitmap: Bitmap?) {
        if (bitmap != null) {
            cache.put(key, bitmap)
        }
    }

    fun get(key: String?): Bitmap? = cache.get(key)

    private fun anEighthOfMemory() = ((Runtime.getRuntime().maxMemory() / 1024).toInt() / 8)

    fun generateBitmapId(
        url: String?,
        contentWidth: Int,
        contentHeight: Int
    ) = StringBuilder().append(url).append(contentWidth).append(contentHeight).toString()
}
```
