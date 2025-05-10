
# Laboratorio 8. Uso de la galería


El flujo del ejemplo será:

- Partimos del laboratorio anterior donde ya tenemos resuelto el login. Si no lo tiene puede clonarlo aqui

```
https://github.com/Domiciano/MOV251Lab7
```

- Luego del login, aterrizamos en una pantalla que nos permita seleccionar una foto y además tenga un `AsyncImage`.

- Al seleccionarla, subimos automáticamente la foto al bucket

- Cargamos la imagen en el `AsyncImage` desde el bucket

- Se almacena relaciona la foto con el usuario

- La próxima vez que inicie sesión el usuario, debe ver la foto que escogió


# Vista

```kt
@Composable
fun ProfileScreen( ... ) {
    
	...

	val imageURL by viewmodel.imageURL.collectAsState()

	...


    Scaffold { innerPadding ->
        Column(modifier = Modifier.fillMaxSize().padding(innerPadding)) {
            
			AsyncImage(
                model = imageURL,
                contentDescription = "",
                contentScale = ContentScale.Crop,
                modifier = Modifier
                    .fillMaxWidth()
                    .aspectRatio(1f)
                    .clip(CircleShape)
            )

            Button(onClick = { ... }) {
                Text("Cambiar foto")
            }
            
            
        }    
    }

}
```


# Escoger foto de la galería

Se debe declarar un launcher de galería para este fin

```kt
val pickImageLauncher = rememberLauncherForActivityResult(
	contract = ActivityResultContracts.GetContent()
) { uri: Uri? ->
	...
}
```

Esa uri que retona es clave porque habrá que resolver la URI para poder obtener el stream de bytes para enviarlos por internet.


Ya con el launcher podemos invocarlo

```kt
Button(onClick = { pickImageLauncher.launch("image/*") }) {
	Text("Cambiar foto")
}
```


# Content Resolver

Necesitamos ahora alguna forma de convertir los `Uri` en `MultipartBody.Part` que es el formato en el que directus nos pide que enviemos las fotos.

Podemos tratar el problema por medio de patrones de diseño Singleton y Service Locator. Para eso crearemos una clase de acceso `FileProvider` y la lógica la haremos en `FileSource` similar a como lo hemos hecho con `DataStore`.



```kt
import android.content.Context
import android.net.Uri
import okhttp3.MediaType.Companion.toMediaTypeOrNull
import okhttp3.MultipartBody
import okhttp3.RequestBody.Companion.toRequestBody
import java.io.IOException
import java.util.UUID

object FileProvider {

    private var instance: FileSource? = null

    fun init(context: Context) {
        if (instance == null) {
            instance = FileSource(context)
        }
    }

    fun get(): FileSource {
        return instance ?: throw IllegalStateException("LocalDataStore is not initialized")
    }

}

class FileSource(val context: Context) {

    fun prepareMultipartFromUri(uri: Uri): MultipartBody.Part {
        val contentResolver = context.contentResolver
        val inputStream = contentResolver.openInputStream(uri) ?: throw IOException("invalid URI")
        val fileName = UUID.randomUUID().toString()
        val mimeType = contentResolver.getType(uri) ?: "application/octet-stream"
        val requestBody = inputStream.readBytes().toRequestBody(mimeType.toMediaTypeOrNull())
        return MultipartBody.Part.createFormData("file", fileName, requestBody)
    }

}

```

No olvidemos de inicializar la clase

```kt
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        FileProvider.init(applicationContext)
        ...
        setContent {
            ...
        }
    }
}
```

De aquí en adelante podemos usar el content resolver en cualquier sección de la aplicación



# Lógica de upload


Podemos enviar fotos al endpoint

```http
POST /files
```

Añadiendo Multipart Data al body del request. El endpoint está protegido y requiere `Authorization` para usarlo.



El endpoint se puede escribir así en `Retrofit`


```kt
@Multipart
@POST("files")
suspend fun uploadFile(
	@Header("Authorization") authHeader: String,
	@Part file: MultipartBody.Part
): Response< ... >
```

Donde la respuesta es del tipo

```json
{
  "data": {
    "id": "0005d4cc-6d0c-4e92-8aac-fa00e8ffc196",
    "filename_download": "foto_perfil.jpg"
  }
}
```

*Cree los data classes para recibir la información*


Gracias a nuestro Service Locator, ahora podemos crear los `@Part` usando

```kt
val part = FileProvider.get().prepareMultipartFromUri(uri)
```

En su capa lógica

# Actualización de nombre

Debe evitar que su fotografía se guarde con el nombre con el que está almacenada en su celular. La razón es que puede sufrir reemplazos de fotos inesperadas. Por esta razón debe usar el ID que devuelve el servicio.

De modo que debe hacer un request a 

```http
PATCH /files/0005d4cc-6d0c-4e92-8aac-fa00e8ffc196
```

Usando un body como

```json
{
  "title": "0005d4cc-6d0c-4e92-8aac-fa00e8ffc196",
  "filename_download": "0005d4cc-6d0c-4e92-8aac-fa00e8ffc196"
}
```

De esta manera unifica tanto el título como el nombre del archivo a descargar.


En Retrofit se puede tener algo como

```kt
@PATCH("files/{id}")
suspend fun updateFileMetadata(
	@Header("Authorization") auth: String,
	@Path("id") fileId: String,
	@Body body: FileUpdateRequest
): Response<...>
```

La respuesta esperada es la misma que el endpoint de upload

```json
{
  "data": {
    "id": "0005d4cc-6d0c-4e92-8aac-fa00e8ffc196",
    "filename_download": "0005d4cc-6d0c-4e92-8aac-fa00e8ffc196"
  }
}
```






