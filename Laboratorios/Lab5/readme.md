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


# Vista

No saque todo su arsenal de components de Jetpack Compose, sólo use el %1 de su poder, para crear la vista mínima necesaria para crear una pantalla para este laboratorio.

La pantalla se debe componer de un campo de texto donde el usuario podrá escribir lo que busca

Tendrá un botón que permitirá realizar la búsqueda

Y tendrá un LazyColumn para representar los resultados de la búsqueda.

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

Este modelo está creado a propósito para que entienda la distinción entre un modelo de transporte (DTO) y un modelo de dominio

# Capas

Considere esta estructura de carpetas

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

Cree el `ViewModel` donde modele las variables que serán visibles.

Cree el `Repository` donde usará el `DataSource` y resolverá la recepción de datos y respectiva transformación para darle los datos que requere la cada de `ViewModel`

Cree el `DataSource` donde hará el llamado HTTP al API de Deezer usando como entrada el string búsqueda.


# Instalación de dependencias de Retrofit

Instale la dependencia de Retrofit

```kotlin
val retrofitVersion = "2.11.0"
implementation("com.squareup.retrofit2:retrofit:$retrofitVersion")
implementation("com.squareup.retrofit2:converter-gson:$retrofitVersion")
```





```kotlin
class RetrofitConfiguration {
    companion object {
        var retrofit: Retrofit = Retrofit.Builder()
            .baseUrl("https://facelogprueba.firebaseio.com")
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }
}
```
