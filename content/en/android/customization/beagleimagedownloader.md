---
title: BeagleImageDownloader
weight: 121
description: >-
  In this section, you will find the description of the BeagleImageDownloader class and details of the
   methods that implement it.
---

---

## Introduction

You can register how remote images will be downloaded and populated in **ImageView** by providing a class that implements this logic, which was added to Beagle 1.3.0

With **BeagleImageDownloader** being created in the frontend of your Android application, Beagle will know which logic to use to download and configure the **Image** components with **ImagePath.Remote**.

### Step 1: Add the dependencies

Locate the file `build.gradle (Module: app),` open it and scroll down until you find the **`dependencies {}`** code block.

1. Copy and paste the line below into the dependencies:

   * _implementation 'androidx.core:core-ktx:1.2.0'_
   * _implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.9'_
   * _implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.2.0'_

   \_\_

2. Click **Sync now** to synchronize Gradle again.
3. This dependency is needed, as the class that implements `BeagleImageDownloader` will import some settings from it.

### Step 2: Create the class

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
