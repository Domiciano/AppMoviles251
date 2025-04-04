# Consumo de RestAPI

El objetivo de este laboratorio es que usted consuma una public Rest API. Vamos a explorar cómo se puede hacer la transformación de datos y cómo llega desde internet a los ojos del usuario.




# Entendiendo el API de Deezer

Vamos a usar específicamente el servicio de búsqueda de Deezer. Usamos esta API porque la de Spotify si bien es pública requiere un token y ese procedimiento no es el foco de este laboratorio

La URL es 

```
https://api.deezer.com/search?q=eminem
```

donde usted puede cambiar eminem por lo que usted requiera buscar. El API de Deezer responderá con la siguiente estructura

```json
{
  "data": [
    {
      "id": 916424,
      "title": "Without Me",
      "link": "https://www.deezer.com/track/916424",
      "duration": 290,
      "explicit_lyrics": true,
      "preview": "https://cdnt-preview.dzcdn.net/api/1/1/a/e/b/0/aeb58f2f63ee57fb9c47cbe8fb5ccdaa.mp3",
      "artist": {
        "id": 13,
        "name": "Eminem",
        "link": "https://www.deezer.com/artist/13",
        "picture": "https://api.deezer.com/artist/13/image"
      },
      "album": {
        "id": 103248,
        "title": "The Eminem Show",
        "cover": "https://api.deezer.com/album/103248/image"
      }
    }
  ],
  "total": 1
}
```

🎯 Este objeto debería ser modelado para poder ser recibido por la aplicación en la capa de `DataSource`. El modelo de este mensaje tiene la definición de DTO.

🎯 La capa de `DataSource` emite valores a la capa `Repository`. Emita un DTO a la capa de `Repository`.


# Vista

No saque todo su arsenal de components de Jetpack Compose, sólo use el %1 de su poder para crear la vista mínima necesaria para crear una pantalla para este laboratorio.

🎯 La pantalla se debe componer de un campo de texto donde el usuario podrá escribir lo que busca

🎯 Debe tener un botón que permitirá realizar la búsqueda

🎯 Debe tener un LazyColumn para representar los resultados de la búsqueda.

# Modelo

El modelo de la aplicación no requiere que tenga todo lo que nos entrega el modelo de Deezer. De hecho esta diferencia es la misma de los DTO (Data Transfer Object) y un Domain Model.

Lo que nos entrega Deezer es un DTO, pero para la lógica de nuestra aplicación, podemos crear un objeto únicamente con los valores que nos interesan.

El modelo de la aplicación puede ser 


```kotlin
data class Track(
    val name: String,
    val duration: Int,
    val artistName: String,
    val artistPicture: String,
    val album: String,
    val albumCover: String
)
```

🎯 Cree la clase de modelo

Este modelo está creado a propósito para que entienda la distinción entre un modelo de transporte (DTO) y un modelo de dominio

# Capas

🎯 Considere esta estructura de carpetas

```
📂 project  
├── 📂 features  
│   ├── 📂 musicSearch
│   │   ├── 📂 data  
│   │   │   ├── 📂 repositories  
│   │   │   ├── 📂 dataSources
│   │   │   ├── 📂 dto 
│   │   ├── 📂 ui  
│   │   │   ├── 📂 viewModel  
│   │   │   ├── 📂 components  
│   │   │   ├── 📂 screens  
├── 📂 domain  
│   ├── 📂 model
│   ├── 📄 MainActivity.kt
```

🎯 Cree el `ViewModel` donde modele las variables que serán visibles.

🎯 Cree el `Repository` donde usará el `DataSource` y resolverá la recepción de datos y respectiva transformación para darle los datos que requere la cada de `ViewModel`

🎯 Cree el `DataSource` donde hará el llamado HTTP al API de Deezer usando como entrada el string búsqueda.


# Utilidades para HTTP

Puede hacer uso de esta clase de utilidades, pero tenga en cuenta que evolucionaremos para luego usar `Retrofit`

```kotlin
import java.net.URL
import javax.net.ssl.HttpsURLConnection

class HTTPUtil {
    fun GETRequest(url: String): String {
        val url = URL(url)
        val client = url.openConnection() as HttpsURLConnection
        client.requestMethod = "GET"
        return client.inputStream.bufferedReader().readText()
    }

    fun PUTRequest(url: String, json: String): String {
        val url = URL(url)
        val client = url.openConnection() as HttpsURLConnection
        client.requestMethod = "PUT"
        client.setRequestProperty("Content-Type", "application/json")
        client.doOutput = true
        client.outputStream.bufferedWriter().use {
            it.write(json)
            it.flush()
        }
        return client.inputStream.bufferedReader().readText()
    }

    fun POSTRequest(url: String, json: String): String {
        val url = URL(url)
        val client = url.openConnection() as HttpsURLConnection
        client.requestMethod = "POST"
        client.setRequestProperty("Content-Type", "application/json")
        client.doOutput = true
        client.outputStream.bufferedWriter().use {
            it.write(json)
            it.flush()
        }
        return client.inputStream.bufferedReader().readText()
    }

    fun PATCHRequest(url: String, json: String): String {
        val url = URL(url)
        val client = url.openConnection() as HttpsURLConnection
        client.requestMethod = "PATCH"
        client.setRequestProperty("Content-Type", "application/json")
        client.doOutput = true
        client.outputStream.bufferedWriter().use {
            it.write(json)
            it.flush()
        }
        return client.inputStream.bufferedReader().readText()
    }


    fun DELETERequest(url: String): String {
        val url = URL(url)
        val client = url.openConnection() as HttpsURLConnection
        client.requestMethod = "DELETE"
        return client.inputStream.bufferedReader().readText()
    }

}
```

🎯 Consuma los datos del API y muestre el resultado en la capa de la vista

# Resultado esperado

Se espera que el usuario pueda buscar una canción en Deezer y que los resultados de búsqueda se puedan observar mediente una lista. Puede incluir la fotografía del album o la fotografía del artista que Deezer provee, además claro del nombre de la canción, el nombre del artista y el nombre del album

# Instalación de dependencias de Retrofit

Instale la dependencia de Retrofit

```kotlin
val retrofitVersion = "2.11.0"
implementation("com.squareup.retrofit2:retrofit:$retrofitVersion")
implementation("com.squareup.retrofit2:converter-gson:$retrofitVersion")
```

Una vez tengamos retrofit, podemos crear un objeto de configuration en la ruta `configuration/RetrofitConfiguration.kt`


```kotlin
object RetrofitConfiguration {
    var deezerRetrofit: Retrofit = Retrofit.Builder()
        .baseUrl("https://api.deezer.com/")
        .addConverterFactory(GsonConverterFactory.create())
        .build()
}
```

El `object` es un descriptor de clase tipo Singleton en Kotlin.

Una vez con el objeto `deezerRetrofit`, podemos hacer clases de acceso a datos, que Retrofit establece como `Services`. En nuestro caso son `DataSources`.

```kotlin
interface SearchService {
    @GET("/search")
    suspend fun search(@Query("q") query: String): DTOClass
}
```

Aquí, las `suspend` functions, son funciones que sólo pueden ejecutarse dentro de una `corutina`

Finalmente, cuando tenga definido su acceso a datos, puede inyectarlo como dependencia en la clase de tipo repository

```kotlin
class RepositoryClass(
    private val searchService: SearchService = RetrofitConfiguration.deezerRetrofit.create(SearchService::class.java)
) {
...
}
```

Con esta inyección, ya está lista para obtener elementos de tipo `DTOClass` acorde a su problema
