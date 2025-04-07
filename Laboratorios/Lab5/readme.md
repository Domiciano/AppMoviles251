

```kotlin
    implementation("androidx.datastore:datastore-preferences:1.1.4");
    implementation("com.google.code.gson:gson:2.12.1")
    implementation("androidx.navigation:navigation-compose:2.7.7")
    implementation("com.squareup.retrofit2:retrofit:2.11.0")
    implementation("com.squareup.retrofit2:converter-gson:2.11.0")
```


# Instalación



1. Cree un archivo llamado `docker-compose.yml` con el siguiente contenido:

```yml
version: "3"
services:
  db:
    image: postgres:17
    environment:
      POSTGRES_DB: directus
      POSTGRES_USER: directus
      POSTGRES_PASSWORD: directus
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U directus -d directus"]
      interval: 10s
      retries: 5
      start_period: 10s
      timeout: 5s

  directus:
    image: directus/directus:11.5.0
    ports:
      - "8055:8055"
    volumes:
      - directus_database:/directus/database
      - directus_uploads:/directus/uploads
      - directus_extensions:/directus/extensions
    environment:
      SECRET: "alfabeta"
      ADMIN_EMAIL: "domic.rincon@gmail.com"
      ADMIN_PASSWORD: "alfabeta"
      DB_CLIENT: "pg"
      DB_HOST: "db"
      DB_PORT: "5432"
      DB_DATABASE: "directus"
      DB_USER: "directus"
      DB_PASSWORD: "directus"
      WEBSOCKETS_ENABLED: "true"
      ACCESS_TOKEN_TTL: "3600"
    depends_on:
      db:
        condition: service_healthy

volumes:
  db_data:
  directus_database:
  directus_uploads:
  directus_extensions:
```

2. Inicie solo la base de datos:

```bash
docker-compose up -d
```

# Entrando a la base de datos

Primero entremos a nuestro contenedor de base de datos

```
docker ps
```

Allí verá listados sus contenedores. Ahora ingrese al contenedor de base de datos usando

```
docker exec -it directus_db_1 bash
```


Estando dentro puede usar el comando `psql` para lo que requiera, entre otras, crear tablas de prueba.

Si bien la mejor opción es tener ya de una vez su modelo de datos en un archivo `model.sql`, vamos a crear una tabla llamada `user_profiles`.


Entremos al servicio de base de datos

```
psql -h localhost -U directus -d directus
```


Puede listar sus tablas con

```
\dt
```

Luego cree su tabla

```sql
CREATE TABLE user_profile (
    id SERIAL PRIMARY KEY,
    user_id UUID UNIQUE REFERENCES directus_users(id) ON DELETE CASCADE,
    address TEXT,
    phone TEXT,
    birthdate DATE
);
```

# Permisos públicos para registro

En el dashboard de directus vamos a habilitar como público el permiso de crear en la tabla `directus_users`.

Adicionalmente permitamos los permisos de lectura sobre la tabla `directus_roles`

# Creando role y obteniendo su UUID

En el dashboard de directus vamos a crear los roles de nuestra app. Luego de eso, vamos a hacer un GET a roles

```
http://localhost:8055/roles
```

La respuesta es parecida a

```json
{
    "data": [
        {
            "id": "5e240c66-5855-4e7a-90b1-e458eff2e152",
            "name": "Administrator",
            "icon": "verified",
            "description": "$t:admin_description",
            "parent": null,
            "children": []
        },
        {
            "id": "cb1138ed-ef65-403e-9fd4-18a39e425279",
            "name": "Driver",
            "icon": "supervised_user_circle",
            "description": null,
            "parent": null,
            "children": []
        }
    ]
}
```

Usemos el `id` del nuevo rol.

Finalmente ya somos capaces de crear un usuario

# Crear el usuario

Haga POST a

```
http://localhost:8055/users
```

Body

```json
{
  "email": "a@a.com",
  "password": "contrasena12345",
  "first_name": "Nuevo",
  "last_name": "Usuario",
  "role":"cb1138ed-ef65-403e-9fd4-18a39e425279"
}
```

Donde `role` debe usar el `id` del rol correspondiente

La repuesta esperada es `204 No Content`


Tenga en cuenta que aquí solo está registrando al usuario, pero si requiere registrar más datos del usuario de acuerdo a la lógica de negocio, allí necesita la tabla `user_profiles`.

Primero requiere un `token` así que vamos a loggearnos

# Login

Haga POST a 

```
http://localhost:8055/auth/login
```

Body

```json
{
    "data": {
        "expires": 3600,
        "refresh_token": "oRz7sg0lDc3Rq6M_f5TPMFw78yKhvUGUnsauTYXu0cUP8n5HqW2IV9-to4ltCWkx",
        "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjVmMTFlZmQ4LTFjMDItNDhhOC1iNTc5LWM2NzA0NzIyNTI3NSIsInJvbGUiOiJjYjExMzhlZC1lZjY1LTQwM2UtOWZkNC0xOGEzOWU0MjUyNzkiLCJhcHBfYWNjZXNzIjpmYWxzZSwiYWRtaW5fYWNjZXNzIjpmYWxzZSwiaWF0IjoxNzQ0MDM5NzEzLCJleHAiOjE3NDQwNDMzMTMsImlzcyI6ImRpcmVjdHVzIn0.TNsbmkGiOHP-j5TIFoaHyJobFUTHuZkNC5YhEy9jfq0"
    }
}
```

Su token será el `access_token`


# Conocer su ID de usuario dentro de la app

Para algunas funciones usted requerirá tener su ID. Para obtenerlo haga GET a

```
http://localhost:8055/users/me
```

Usando como header

```
Authorization: Bearer <SU TOKEN>
```

La respuesta esperada es

```json
{
    "data": {
        "id": "6700f0c3-8973-4019-9cc5-63008ecf6974"
    }
}
```

# Registrar información del usuario

Finalmente ya podemos registrar información del usuario.  

Haga POST a

```
http://localhost:8055/items/user_profile
```

Body

```json

```


# Almacenamiento de datos en el datastore

Importémosnos

```kotlin
implementation("androidx.datastore:datastore-preferences:1.1.4");
implementation("com.google.code.gson:gson:2.12.1")
```


Una vez con estos datos, podemos almacenar los datos. Para esto almacenémoslos en JSON

```kotlin
val json = gson.toJson(userProfile)
```

Luego usemos el `dataStore`

```kotlin
context.dataStore.edit { prefs ->
    prefs[stringPreferencesKey("user_profile")] = json
}
```

Y cuando se requiera llamar lo almacenado

```kotlin
val profileFlow = context.dataStore.data.map { prefs ->
    val json = prefs[stringPreferencesKey("user_profile")]
    gson.fromJson(json, UserProfile::class.java)
}
```


# DataStore

En el top level de la app, defina
```kotlin
val Context.dataStore: DataStore<Preferences> by preferencesDataStore(name = "appvariables")
```

Realicemos un singleton para acceder a la memoria

```kotlin
object LocalDataSourceProvider {

    private var instance: LocalDataSource? = null

    fun init(context: Context) {
        if (instance == null) {
            instance = LocalDataSource(context.applicationContext.dataStore)
        }
    }

    fun get(): LocalDataSource {
        return instance ?: throw IllegalStateException("LocalDataSource not initialized. Call LocalDataSourceProvider.init(context) first.")
    }
}
```

Donde el `LocalDataSource` es 

```kotlin
class LocalDataSource(private val dataStore: DataStore<Preferences>) {

    suspend fun saveProfile(key: String, value: String) {
        dataStore.edit { prefs ->
            prefs[stringPreferencesKey(key)] = value
        }
    }

    fun getProfile(key: String): Flow<String> = dataStore.data.map { prefs ->
        prefs[stringPreferencesKey(key)] ?: ""
    }
}
```




# Configurar expiracion de token

El tiempo de expiración del token se cambia usando una variable de entorno que puede agregar en su `docker-compose.yml

```
ACCESS_TOKEN_TTL=1h
```

De igual manera el refresh token, que por defecto tiene 7 días.

```
REFRESH_TOKEN_TTL=7d
```


# Refrescar el token

Haga POST a 

```
http://localhost:8055/auth/refresh
```

Body

```json
{
  "refresh_token": "def456..."
}
```

Respuesta esperada

```json
{
  "access_token": "nuevo_access_token...",
  "refresh_token": "nuevo_refresh_token...",
  "expires": 900
}
```
