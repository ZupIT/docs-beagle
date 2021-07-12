---
title: Gerenciar Cache
weight: 113
description: >-
  Nesta seção, você encontra informações sobre como gerenciar cache do beagle e comomodificá-la. 
  modificá-la.
---

---

## Introdução

A interface StoreHandler define um protocolo que permite personalizar a forma como o cache é manipulado no banco de dados e na memória. 

```kotlin
interface StoreHandler {
    
    fun save(storeType: StoreType, data: Map<String, String>)

    fun restore(storeType: StoreType, vararg keys: String): Map<String, String?>

    fun delete(storeType: StoreType, key: String)

    fun getAll(storeType: StoreType): Map<String, String>
}
```

O enum StoreType define, por meio de atributos, se os dados serão manipulados em disco ou na memória. 

   * `DATABASE` representa o disco.
   * `MEMORY` representa a memória.

```kotlin
enum class StoreType {
   
    DATABASE,

    MEMORY
}
```

A interface LocalStore permite mapear as ações de salvar, restaurar, deletar e selecionar tudo.

Essa interface permite você criar regras de como os dados serão manipulados em uma classe. Por exemplo: você pode criar uma classe e usar a interface para fazer uma regra de como os dados serão manipulado em disco.
exemplo criar uma classe e usar essa interface para fazer uma regra de como os dados serão manipulado em disco.

```kotlin
interface LocalStore {
    fun save(key: String, value: String)
    fun restore(key: String): String?
    fun delete(key: String)
    fun getAll(): Map<String, String>
}
```

## Como criar uma camada de cache customizada?

Para criar uma camada de cache customizada, siga os próximos passos:

### Passo 1: Criar o object MemoryLocalStore

É necessário você criar duas classes que implementem a interface `LocalStore`: a MemoryLocalStore e a DatabaseLocalStore. 

Com essas interfaces, você pode mapear as ações de salvar, restaurar, deletar e selecionar tudo, como mostra o exemplo abaixo:


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

### Passo 2: Criar a classe DatabaseLocalStore

Agora, você deve criar uma segunda classe que, para isso, depende da criação do StoreHandler, que é a DatabaseLocalStore. Esse é o mesmo arquivo declarado com algumas classes adjacentes que servem para a classe DatabaseLocalStore, mas que poderiam estar em arquivos diferentes, se você quiser. 

Veja como funciona no exemplo a seguir:

O arquivo abaixo possui as configurações para o SQL Lite e também as definições de como as ações de manipulação de cache funcionarão, no caso da persistência do cache no banco de dados.

No atributo database da classe DatabaseLocalStore, a classe BeagleDatabaseManager é passada, chamando o método getDatabase e dentro do parâmetro você deve passar o contexto do aplicativo.

Siga o exemplo no atributo da classe DatabaseLocalStore abaixo:


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

### Passo 3: Criar a classe StoreHandlerDefault

A classe StoreHandler define um protocolo que permite personalizar a forma como o cache é manipulado no banco de dados e na memória. 

Após a definição das classes `MemoryLocalStore` e `DatabaseLocalStore`, você pode definir a `StoreHandler`. Veja o exemplo abaixo:


```kotlin
import br.com.zup.beagle.android.annotation.BeagleComponent
import br.com.zup.beagle.android.store.StoreHandler
import br.com.zup.beagle.android.store.StoreType

@BeagleComponent
internal class StoreHandlerDefault(
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
