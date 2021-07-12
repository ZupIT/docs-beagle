---
title: Manage Cache
weight: 109
description: >-
  In this section, you will find information about the persistence layer of Beagle's cache and how to modify it.
---

---

## Introduction

The StoreHandler interface defines a protocol that allows you to customize how the cache is handled in the database and in the memory.

```kotlin
interface StoreHandler {
    
    fun save(storeType: StoreType, data: Map<String, String>)

    fun restore(storeType: StoreType, vararg keys: String): Map<String, String?>

    fun delete(storeType: StoreType, key: String)

    fun getAll(storeType: StoreType): Map<String, String>
}
```

The StoreType enum defines through attributes whether data will be manipulated on disk or memory

   * `DATABASE` represents the disk.
   * `MEMORY` represents memory.

```kotlin
enum class StoreType {
   
    DATABASE,

    MEMORY
}
```

LocalStore interface allows you to map the actions of saving, restoring, deleting and the getting all.

This interface allows you to create rules for how data will be handled in a class.
For example: create a class and use this interface to make a rule of how data will be handled on the disk.

```kotlin
interface LocalStore {
    fun save(key: String, value: String)
    fun restore(key: String): String?
    fun delete(key: String)
    fun getAll(): Map<String, String>
}
```

## How to create a custom cache manage

To create a custom cache manage, just follow the next steps:

### Step 1: Create an object MemoryLocalStore

First, it is necessary to create two classes that will implement the `LocalStore` interface: the MemoryLocalStore and the DatabseLocalStore. 

With the LocalStore interface, you to map the actions of saving, restoring, deleting and getAll, as shown on the example below:


```kotlin
internal object MemoryLocalStore : LocalStore {

    private val cache = mutableMapOf<String, String>()

    override fun save(key: String, value: String) {
        cache[key] = value
    }

    override fun restore(key: String): String? {
        return cache[key]
    }

    override fun delete(key: String) {
        cache.remove(key)
    }

    override fun getAll(): Map<String, String> {
        return cache.toMap()
    }
}
```

### Step 2: Create a class DatabaseLocalStore

Now, you have to create a second class that depends on the StoreHandler creation, which is a `DatabaseLocalStore`. This is the same file declared with some adjacent classes that work to this `DatabaseLocalStore` class, but it could be in different files, if you want. 

See how it works on the following example: 

The file below has some SQL Lite configuration and definition on how the cache manipulation actions will work, in case of a cache persistence on the database. 

In the database attribute of the `DatabaseLocalStore` class, you pass the BeagleDatabaseManager class, so it's called the getDatabase method and within the parameter you must pass the application context of the project. 

Follow the example in the DatabaseLocalStore class attribute below:


```kotlin
internal object ScreenEntry : BaseColumns {
    const val TABLE_NAME = "KeyValueCache"
    const val KEY_COLUMN_NAME = "key"
    const val VALUE_COLUMN_NAME = "value"
}

internal class DatabaseLocalStore(
    private val contentValuesFactory: ContentValuesFactory = ContentValuesFactory(),
    private val database: SQLiteDatabase = BeagleDatabaseManager.getDatabase(
        BeagleUiSampleApplication.instance)
) : LocalStore {

    override fun save(key: String, value: String) {
        val values = contentValuesFactory.make().apply {
            put(ScreenEntry.KEY_COLUMN_NAME, key)
            put(ScreenEntry.VALUE_COLUMN_NAME, value)
        }


        val newRowId = database.insertWithOnConflict(ScreenEntry.TABLE_NAME, null, values,
            SQLiteDatabase.CONFLICT_REPLACE)
        if (newRowId == -1L) {
            BeagleMessageLogs.logDataNotInsertedOnDatabase(key, value)
        }
    }

    override fun restore(key: String): String? {
        return executeRestoreQueryForKey(key).use { cursor ->
            if (cursor.count > 0) {
                cursor.moveToFirst()
                cursor.getString(cursor.getColumnIndexOrThrow(ScreenEntry.VALUE_COLUMN_NAME))
            } else {
                null
            }
        }
    }

    override fun delete(key: String) {
        database.delete(ScreenEntry.TABLE_NAME, "${ScreenEntry.KEY_COLUMN_NAME}=?", arrayOf(key))
    }

    override fun getAll(): Map<String, String> {
        val columnsToReturn = arrayOf(ScreenEntry.KEY_COLUMN_NAME, ScreenEntry.VALUE_COLUMN_NAME)
        val columnsForWhereClause = ""
        val valuesForWhereClause = arrayOf<String>()
        val cursor = database.query(
            ScreenEntry.TABLE_NAME,
            columnsToReturn,
            columnsForWhereClause,
            valuesForWhereClause,
            null,
            null,
            null
        )

        val returnMap = mutableMapOf<String, String>()
        if (cursor.count > 0) {
            cursor.moveToFirst()
            while (!cursor.isAfterLast) {
                returnMap[cursor.getString(cursor.getColumnIndexOrThrow(ScreenEntry.KEY_COLUMN_NAME))] =
                    cursor.getString(cursor.getColumnIndexOrThrow(ScreenEntry.VALUE_COLUMN_NAME))

                cursor.moveToNext()
            }
        }
        cursor.close()

        return returnMap
    }

    private fun executeRestoreQueryForKey(key: String): Cursor {
        val columnsToReturn = arrayOf(ScreenEntry.VALUE_COLUMN_NAME)
        val columnsForWhereClause = "${ScreenEntry.KEY_COLUMN_NAME}=?"
        val valuesForWhereClause = arrayOf(key)
        return database.query(
            ScreenEntry.TABLE_NAME,
            columnsToReturn,
            columnsForWhereClause,
            valuesForWhereClause,
            null,
            null,
            null
        )
    }
}

internal class ContentValuesFactory {
    fun make(): ContentValues = ContentValues()
}

internal object BeagleDatabaseManager {

    private const val DATABASE_NAME = "BeagleDefaultStore.db"
    private const val DATABASE_VERSION = 2

    private lateinit var database: SQLiteDatabase

    fun getDatabase(context: Context): SQLiteDatabase {
        if (!::database.isInitialized) {
            database = BeagleSQLiteDatabase(
                context,
                DATABASE_NAME,
                DATABASE_VERSION
            ).writableDatabase
        }
        return database
    }
}

internal open class BeagleSQLiteDatabase(
    context: Context,
    databaseName: String,
    databaseVersion: Int
) : SQLiteOpenHelper(
    context,
    databaseName,
    null,
    databaseVersion
) {
    override fun onCreate(db: SQLiteDatabase?) {
        val createTableQuery = "CREATE TABLE ${ScreenEntry.TABLE_NAME} (" +
                "${BaseColumns._ID} INTEGER PRIMARY KEY," +
                "${ScreenEntry.KEY_COLUMN_NAME} TEXT NOT NULL UNIQUE," +
                "${ScreenEntry.VALUE_COLUMN_NAME} TEXT NOT NULL" +
                ")"
        db?.execSQL(createTableQuery)
    }

    override fun onUpgrade(db: SQLiteDatabase?, oldVersion: Int, newVersion: Int) {
        val deleteTableQuery = "DROP TABLE IF EXISTS ${ScreenEntry.TABLE_NAME}"
        db?.execSQL(deleteTableQuery)
        onCreate(db)
    }
}

internal object BeagleMessageLogs {

    fun logDataNotInsertedOnDatabase(key: String, value: String) {
        BeagleLoggerDefault().warning(
            "Error when trying to insert key=$key with value=$value on Beagle default database."
        )
    }
}
```

### Step 3: Create a StoreHandlerDefault class

The StoreHandler class defines a protocol that allows you to customize how the cache is handled in the database and in memory.

After the `MemoryLocalStore` and `DatabaseLocalStore` classes definition, you can define `StoreHandler`. See how in the example below: 


```kotlin
import br.com.zup.beagle.android.annotation.BeagleComponent
import br.com.zup.beagle.android.store.StoreHandler
import br.com.zup.beagle.android.store.StoreType

@BeagleComponent
internal class StoreHandler(
    private val memoryLocalStore: MemoryLocalStore = MemoryLocalStore,
    private val databaseLocalStore: DatabaseLocalStore = DatabaseLocalStore()
) : StoreHandler {

    override fun save(storeType: StoreType, data: Map<String, String>) {
        data.forEach {
            if (storeType == StoreType.DATABASE) {
                databaseLocalStore.save(it.key, it.value)
            } else {
                memoryLocalStore.save(it.key, it.value)
            }
        }
    }

    override fun restore(storeType: StoreType, vararg keys: String): Map<String, String?> {
        val values = mutableMapOf<String, String?>()
        keys.forEach {
            val value = if (storeType == StoreType.DATABASE) {
                databaseLocalStore.restore(it)
            } else {
                memoryLocalStore.restore(it)
            }
            values[it] = value
        }
        return values
    }

    override fun delete(storeType: StoreType, key: String) {
        if (storeType == StoreType.DATABASE) {
            databaseLocalStore.delete(key)
        } else {
            memoryLocalStore.delete(key)
        }
    }

    override fun getAll(storeType: StoreType): Map<String, String> {
        return if (storeType == StoreType.DATABASE) {
            databaseLocalStore.getAll()
        } else {
            memoryLocalStore.getAll()
        }
    }
}
```
