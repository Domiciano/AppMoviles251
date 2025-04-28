# Laboratorio 7. Web Sockets

Vamos a montar websockets en directus. Para seguir este laboratorio bien puede usar una de las collections que ya tenga en su modelo o bien puede crear una tabla de `messages`


# Montaje inicial

Debemos partir de un proyecto que tenga resuelta la autenticación en directus ya que requerimos permisos para acceder a las `collections`.

Para este startup vamos con la configuración incial. Cree un nuevo proyecto y luego siga estos pasos

### Permisos

Requerimos tanto el permiso de internet como poder usar *clear traffic*

```xml
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET"/>
    ...
    <application 
        ...
        android:usesCleartextTraffic="true"
        ...
    >
        <activity>
            ...
        </activity>
    </application>
</manifest>
```


### Librerias

```kt
implementation("com.squareup.okhttp3:okhttp:4.12.0")
implementation("androidx.datastore:datastore-preferences:1.1.4");
implementation("com.google.code.gson:gson:2.12.1")
implementation("androidx.navigation:navigation-compose:2.7.7")
implementation("com.squareup.retrofit2:retrofit:2.11.0")
implementation("com.squareup.retrofit2:converter-gson:2.11.0")
```

### Capa UI

```kt
import android.content.Context
import android.os.Bundle
import android.util.Log
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.activity.enableEdgeToEdge
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.datastore.core.DataStore
import androidx.datastore.preferences.core.Preferences
import androidx.datastore.preferences.preferencesDataStore
import androidx.navigation.compose.*
import androidx.lifecycle.viewmodel.compose.viewModel
import androidx.navigation.NavController


val Context.dataStore: DataStore<Preferences> by preferencesDataStore(name = "AppVariables")

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        LocalDataSourceProvider.init(applicationContext.dataStore)
        enableEdgeToEdge()
        setContent {
            Lab7PrepTheme {
                val navController = rememberNavController()
                NavHost(navController = navController, startDestination = "login") {
                    composable("login") { LoginScreen(navController) }
                    composable("chat") { ChatScreen() }
                }
            }
        }
    }
}

@Composable
fun LoginScreen(navController: NavController, viewModel: AuthViewModel = viewModel()) {
    var email by remember { mutableStateOf("domic.rincon@gmail.com") }
    var password by remember { mutableStateOf("alfabeta") }

    val authState by viewModel.authState.collectAsState()

    if (authState.state == AUTH_STATE) {
        navController.navigate("chat") {
            launchSingleTop = true
        }
    }

    Column {
        Box(modifier = Modifier.height(200.dp))
        TextField(value = email, onValueChange = { email = it })
        TextField(value = password, onValueChange = { password = it })
        Button(onClick = { viewModel.login(email, password) }) {
            Text(text = "Iniciar sesión")
        }
    }
}

@Composable
fun ChatScreen() {
    //TODO: Pantalla por realizar
}
```

### Capa ViewModel + Repository

```kt
import android.util.Log
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.firstOrNull
import kotlinx.coroutines.launch

//******************************************************************************
//******************************************************************************
//******************************************************************************
class AuthViewModel(
    val authRepository: AuthRepository = AuthRepository()
) : ViewModel(){

    var authState:MutableStateFlow<AuthState> = MutableStateFlow( AuthState() )

    fun login(email:String, pass:String) {
        viewModelScope.launch(Dispatchers.IO) {
            authRepository.login(LoginDTO(
                email, pass
            ))
            authState.value = AuthState(state = AUTH_STATE)
        }
    }
}

var AUTH_STATE = "AUTH"
var NO_AUTH_STATE = "NO_AUTH"
var IDLE_AUTH_STATE = "IDLE_AUTH"

data class AuthState(
    var state:String = IDLE_AUTH_STATE
)

//******************************************************************************
//******************************************************************************
//******************************************************************************

class AuthRepository(
    val authService: AuthService = RetrofitConfig.directusRetrofit.create(AuthService::class.java)
) {
    suspend fun login(loginDTO: LoginDTO){
        val response = authService.login(loginDTO)
        LocalDataSourceProvider.get().save("accesstoken", response.data.access_token)

        var token = LocalDataSourceProvider.get().load("accesstoken").firstOrNull()
        Log.e(">>>", token.toString())
    }

    suspend fun getAccessToken() : String? {
        var token = LocalDataSourceProvider.get().load("accesstoken").firstOrNull()
        return token
    }
}

//******************************************************************************
//******************************************************************************
//******************************************************************************
```

### Capa de service

```kt
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory
import retrofit2.http.Body
import retrofit2.http.GET
import retrofit2.http.Header
import retrofit2.http.POST

//******************************************************************************
//******************************************************************************
//******************************************************************************

interface AuthService {
    @POST("/auth/login")
    suspend fun login(@Body loginDTO: LoginDTO) : LoginResponse

    @GET("/users")
    suspend fun getAllUsers(@Header("Authorization") authorization:String) : UserResponse
}

//******************************************************************************
//******************************************************************************
//******************************************************************************

data class LoginResponse(
    val data: LoginResponseData
)

data class LoginResponseData(
    val access_token:String,
    val refresh_token:String,
)

data class LoginDTO(
    val email:String,
    val password:String
)

data class UserResponse(
    val data : List<UserDTO>
)

data class UserDTO(
    val first_name:String,
    val last_name:String,
    val email: String
)

//******************************************************************************
//******************************************************************************
//******************************************************************************

object RetrofitConfig {
    val directusRetrofit: Retrofit = Retrofit.Builder()
        .baseUrl("http://10.0.2.2:8055")
        .addConverterFactory(GsonConverterFactory.create())
        .build()
}
```

# Persistencia local

```kt
import androidx.datastore.core.DataStore
import androidx.datastore.preferences.core.Preferences
import androidx.datastore.preferences.core.edit
import androidx.datastore.preferences.core.stringPreferencesKey
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.map

object LocalDataSourceProvider{

    private var instance: LocalDataSource? = null

    fun init(dataStore: DataStore<Preferences>){
        if(instance == null){
            instance = LocalDataSource(dataStore)
        }
    }

    fun get():LocalDataSource{
        return instance ?: throw IllegalStateException("LocalDataStore is not initialized")
    }

}

class LocalDataSource(val dataStore: DataStore<Preferences>) {

    suspend fun save(key:String, value:String){
        dataStore.edit { prefs ->
            prefs[stringPreferencesKey(key)] = value
        }
    }

    fun load(key:String): Flow<String> = dataStore.data.map { prefs ->
        prefs[stringPreferencesKey(key)] ?: ""
    }

}
```

Para este punto ya debería tener una aplicación sencilla con Login y además con token almacenado.

Ya con el `token` usted puede presentarlo tanto en la REST API como al módulo de WebSocket.



# Data necesaria

Puede usar una colección que usted ya tenga, o puede usar una nueva colección de mensajes

```sql
CREATE TABLE messages (
    id SERIAL PRIMARY KEY,
    body TEXT,
    author TEXT,
    timestamp TIMESTAMP
);
```

Este comando lo puede insertar directamente en su contenedor de base de datos usando

```sql
psql -U directus -d directus
```


# Configuración

Necesitarremos una librería de websockets en Android

```kt
implementation("com.squareup.okhttp3:okhttp:4.12.0")
```


# Implementación

Debemos abrir un websocket por query que requiramos observar. En el método de observación va la lógica de conexión, que de forma simple sería similar a esto


```kt
import okhttp3.*
import android.util.Log
import org.json.JSONObject

class MessagesLiveDataSource() {
    fun observeMessages() {
        val client = OkHttpClient()
        val request = Request.Builder()
            .url("wss://d791-186-112-68-24.ngrok-free.app/websocket")
            .build()
        client.newWebSocket(
            request,
            MessagesWebSocketListener()
        )
    }
}
```

Marcamos con la nomenclatura similar a esta `LiveMessagesDataSource` para la obtención de datos en vivo.

Para la creación del objeto `Websocket` necesitamos una clase listener que realmente es a donde llegarán los datos en vivo.


```kt
class MessagesWebSocketListener(
    
) : WebSocketListener() {

    override fun onOpen(webSocket: WebSocket, response: Response) {
        
    }

    override fun onMessage(webSocket: WebSocket, text: String) {
        
    }

    override fun onClosing(webSocket: WebSocket, code: Int, reason: String) {
        Log.e(">>>", "Cerrando WebSocket: $code > $reason")
        webSocket.close(1000, null)
    }

    override fun onFailure(webSocket: WebSocket, t: Throwable, response: Response?) {
        Log.e(">>>", "Error WebSocket: ${t.message}")
    }
}
```

# Mecanismo de ping

Las implementaciones modernas de websocket tienen un ws heartbeat que consiste en que el server envía un mensaje de tipo `ping`. Si el cliente no contesta el respectivo `pong`, la conexión se cae. Esto es para optimizar el número de clientes que están conectados al websocket.

Para implementar ese mecanismo, podemos hacer algo como

```kt
override fun onMessage(webSocket: WebSocket, text: String) {
    val json = JSONObject(text)
    val type = json.optString("type")
    if (type == "ping") {
        webSocket.send("""
            { 
                "type": "pong" 
            }
        """.trimIndent())
        Log.e(">>>","Respondido con pong")
    }
}
```

Sencillito


# Probemos lo que llevamos

Vamos a confirmar que nos podamos conectar con el websocket y demás. Para eso generemos ViewModel y Repository respectivos.

*No olvide llamar al método getLiveFlowOfProducts desde la vista*

```kt
class MessagesViewModel(
    val chatRepository: MessagesRepository = MessagesRepository()
) : ViewModel() {

    fun getLiveFlowOfProducts() {
        viewModelScope.launch(Dispatchers.IO) {
            chatRepository.observeMessages()
        }
    }

}

class MessagesRepository(
    val liveProductsDataSource: MessagesLiveDataSource = MessagesLiveDataSource()
) : ViewModel() {

    fun observeMessages(){
        liveProductsDataSource.observeMessages()
    }

}
```



Ahora vamos con el mecanismo de autenticación


# Mecanismo de autenticación

Hay unos ingredientes más que debemos sumar. Por ejemplo, debemos tener token porque debemos implementar el mecanismo de autenticación.

Para lograrlo, debemos primero hacer uso del `LocalDataSource` ya que ahí está el token almacenado.

```kt
class MessagesWebSocketListener(
    val localDataSource: LocalDataSource = LocalDataSourceProvider.get(),
) {
    ...
}
```

Con esto ya podemos enviar un mensaje de autenticación al websocket para poder empezar a pedir datos

```kt
override fun onOpen(webSocket: WebSocket, response: Response) {
        Log.e(">>>", "Connected");

        val token = runBlocking { localDataSource.load("accesstoken").first() }

        val authMessage = """
        {
            "type":"auth", 
            "access_token":"$token"
        }
        """.trimIndent()
        webSocket.send(authMessage)
}
```

Al enviar el mensaje, debe esperar el aprobado de `directus`. Puede usar lo siguiente para recibirlo

```kt
override fun onMessage(webSocket: WebSocket, text: String) {
    ...
    else if (type == "auth") {
        Log.e(">>>", text)
    }
}
```

# Mecanismo de subscription

Finalmente, a lo que vinimos. Debemos implementar el mecanismo de subscripción. Es el que nos permitirá conectarnos a un flujo de mensajes.

Justamente debemos usar un flujo para exponer los datos a lo largo de las capas y que pueda llegar a la capa de la vista.

Dado que únicamente podemos pedir datos hasta que estemos autenticados, debemos usar el resultado de la solicitud de auth del paso anterior para hacer la subscripción.

```kt
override fun onMessage(webSocket: WebSocket, text: String) {
    ...
    else if (type == "auth") {
            val status = json.optString("status")
            if (status == "ok") {
                val queryMessage = """
                    {
                        "type":"subscribe", 
                        "collection":"products", 
                        "query":{
                            "limit":10
                        }
                    }
                """.trimIndent()
                webSocket.send(queryMessage)
            }
    } else if (type == "subscription") {
            Log.e(">>>", text)
    }
}
```

Note que enviamos un `queryMessage`. El server nos enviará un primer mensaje de tipo `subscription` donde vendrán los primeros datos.

Ahí los imprimimos para verificar. Vamos a verificar lo que llevamos de momento con una implementación del ViewModel y del Repository.

# Manejando los flujos

Ahora sí vamos a exponer la data a través de un flujo. Elijamos el tipo `MutableSharedFlow` para emitir valores a lo largo de las capas

```kt
class MessagesLiveDataSource() {

    val messagesFlow = MutableSharedFlow<String>()
    
    fun observeMessages() : Flow<String> {
        ...
        client.newWebSocket(
            request,
            MessagesWebSocketListener(flow = messagesFlow)
        )
        return messagesFlow
    }
}
```

Note que creamos la variable global `messagesFlow` y luego la insertamos como dependencia en MessagesWebSocketListener. Dentro del listener usaremos el flujo para *emitir* los valores.

El listener debería poder recibir el valor

```kt
class MessagesWebSocketListener(
    val localDataSource: LocalDataSource = LocalDataSourceProvider.get(),
    val flow: Flow<String>
) : WebSocketListener() {
    ...
}
```



