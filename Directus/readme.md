# Directus + PostgreSQL

Este repositorio proporciona una configuración lista para usar de Directus con PostgreSQL utilizando Docker Compose.

## Instalación

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
docker-compose up db -d
```

## Preparación del modelo de datos

Debe definir un modelo de datos para PostgreSQL. Ejemplo:

```sql
CREATE TABLE Movie (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    year INT,
    genre VARCHAR(100)
);

CREATE TABLE Actor (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    nationality VARCHAR(100)
);

CREATE TABLE MovieActor (
    movie_id INT NOT NULL,
    actor_id INT NOT NULL,
    PRIMARY KEY (movie_id, actor_id),
    FOREIGN KEY (movie_id) REFERENCES Movie(id) ON DELETE CASCADE,
    FOREIGN KEY (actor_id) REFERENCES Actor(id) ON DELETE CASCADE
);
```

3. Copie el modelo de datos al contenedor:

```bash
docker cp model.sql <container_name>:/model.sql
```

4. Ejecute el script dentro del contenedor:

```bash
docker exec -it <container_name> psql -U directus -d directus -f /model.sql
```

**Nota:** Reemplace `<container_name>` por el nombre del contenedor de la base de datos. Para verificarlo, use:

```bash
docker ps
```

## Autenticación

### 🔹 Inicio de sesión

- **URL:** `http://localhost:8055/auth/login`
- **Método:** `POST`
- **Headers:** `Content-Type: application/json`
- **Body:**
  
  ```json
  {
      "email": "domic.rincon@gmail.com",
      "password": "alfabeta"
  }
  ```

### 🔹 Registro de un rol

- **URL:** `http://localhost:8055/roles`
- **Método:** `POST`
- **Headers:** `Authorization: Bearer <Admin Access Token>`
- **Body:**
  
  ```json
  {
      "name": "Application User"
  }
  ```

### 🔹 Registro de usuario

- **URL:** `http://localhost:8055/users`
- **Método:** `POST`
- **Headers:** `Content-Type: application/json`
- **Body:**
  
  ```json
  {
    "email": "a@a.com",
    "password": "contraseñaSegura123",
    "first_name": "Bob",
    "last_name": "Dylan",
    "role":"17553a15-e2bb-4afc-8144-066eeec8930c"
  }
  ```

### 🔹 Obtener usuarios

- **URL:** `http://localhost:8055/users`
- **Método:** `GET`
- **Headers:** `Authorization: Bearer <Admin Access Token>`

### 🔹 Obtener usuario actual

- **URL:** `http://localhost:8055/users/me`
- **Método:** `GET`
- **Headers:** `Authorization: Bearer <User Access Token>`

### 🔹 Obtener permisos

- **URL:** `http://localhost:8055/permissions/me`
- **Método:** `GET`
- **Headers:** `Authorization: Bearer <User Access Token>`

### 🔹 Obtener rol por ID

- **URL:** `http://localhost:8055/roles/<Role UUID>`
- **Método:** `GET`

## 📌 Notas finales

- Asegúrese de cambiar las credenciales y secretos antes de desplegar en producción.
- Para más configuraciones, visite la documentación oficial de [Directus](https://docs.directus.io/).

---

# Data

## Obtener todos los registros de una colección

> URL
```
http://localhost:8055/items/post
```
> Method
```
GET
```
> Expected response
```

```



## Obtener un registro por ID

> URL
> Method
> Headers
> Body
> Expected response

```bash
GET
```

```
http://localhost:8055/items/post/11
```

## Filtrar por medio de equivalencia en un campo

> URL
> Method
> Headers
> Body
> Expected response

```bash
GET
```

```
http://localhost:8055/items/post?filter[title][_eq]=<String de búsqueda>
```

## Filtrar por medio del operador contains

> URL
> Method
> Headers
> Body
> Expected response

```bash
GET
```

```
http://localhost:8055/items/post?filter[title][_icontains]=<String de búsqueda>
```


## Seleccionar campos a obtener

> URL
> Method
> Headers
> Body
> Expected response

```bash
GET
```

```
http://localhost:8055/items/post?fields=title,body
```


## Paginación

> URL
> Method
> Headers
> Body
> Expected response

```bash
GET
```
```bash
curl --location 'http://localhost:8055/items/post?limit=5&offset=0'
```

## Ordenamiento

> URL
> Method
> Headers
> Body
> Expected response

```bash
GET
```
```bash
curl --location 'http://localhost:8055/items/post?sort=-title'
```

## Búsqueda por cualquier campo
> URL
> Method
> Headers
> Body
> Expected response

```bash
GET
```
```
curl --location 'http://localhost:8055/items/post?search=fa'
```

## Agregar elemento
> URL
> Method
> Headers
> Body
> Expected response

```bash
POST
```

```bash
curl --location 'http://localhost:8055/items/post' \
--header 'Content-Type: application/json' \
--data '{
    "id": 19,
    "title": "Postman",
    "body": "Postman body"
}'
```

## Agregar grupo de elementos

> URL
> Method
> Headers
> Body
> Expected response

```bash
POST
```

```bash
curl --location 'http://localhost:8055/items/post' \
--header 'Content-Type: application/json' \
--data '[
    {
        "id": 21,
        "title": "Postman",
        "body": "Postman body"
    },
    {
        "id": 22,
        "title": "Postman",
        "body": "Postman body"
    }
]'
```

## Cambiar valores de campos
> URL
> Method
> Headers
> Body
> Expected response

```bash
PATCH
```
```bash
curl --location --request PATCH 'http://localhost:8055/items/post/22' \
--header 'Content-Type: application/json' \
--data '{
    "title": "Patched item"
}'
```

## Eliminar elemento

> URL
> Method
> Headers
> Body
> Expected response

```bash
DELETE
```
```bash
curl --location --request DELETE 'http://localhost:8055/items/post/21'
```

# Files
## Subir archivo

> URL
> Method
> Headers
> Body
> Expected response

```bash
POST
```
```bash
curl --location 'http://localhost:8055/files' \
--form 'file=@"/Users/Alfa/profile.png"'
```
### Response
```json
{
    "data": {
        "id": "4f794762-e24d-4d95-9c30-82466a875dbd",
        "storage": "local",
        "filename_disk": "4f794762-e24d-4d95-9c30-82466a875dbd.png",
        "filename_download": "Logo nuevo icesi blanco 2.png",
        "title": "Logo Nuevo Icesi Blanco 2",
        "type": "image/png",
        "folder": null,
        "uploaded_by": null,
        "created_on": "2025-01-22T15:46:24.068Z",
        "modified_by": null,
        "modified_on": "2025-01-22T15:46:24.197Z",
        "charset": null,
        "filesize": "525282",
        "width": 9301,
        "height": 3460,
        "duration": null,
        "embed": null,
        "description": null,
        "location": null,
        "tags": null,
        "metadata": {},
        "focal_point_x": null,
        "focal_point_y": null,
        "tus_id": null,
        "tus_data": null,
        "uploaded_on": "2025-01-22T15:46:24.195Z"
    }
}
```

### Cambiar metadatos del archivo

> URL
> Method
> Headers
> Body
> Expected response

```bash
PATCH
```
```bash
curl --location --request PATCH 'http://localhost:8055/files/616e89a8-5cfd-4c56-a2e8-61d9d26ad20e' \
--header 'Content-Type: application/json' \
--data '{
    "title": "616e89a8-5cfd-4c56-a2e8-61d9d26ad20e",
    "type": "image/png",
    "storage": "local",
    "filename_download": "616e89a8-5cfd-4c56-a2e8-61d9d26ad20e"
}
'
```

## Obtener archivo por ID

> URL
> Method
> Headers
> Body
> Expected response

```bash
GET
```
```bash
curl --location 'http://localhost:8055/assets/616e89a8-5cfd-4c56-a2e8-61d9d26ad20e'
```



# Realtime


## Query completo por medio de Websocket
```javascript
connection.send(
    JSON.stringify({
        type: 'subscribe',
        collection: 'post',
        query: {
            fields: ['id', 'titulo', 'contenido'], // Solo los campos id, titulo y contenido
            filter: { categoria: { _eq: 2 } },    // Filtrar donde la categoría es 2
            sort: ['-fecha'],                      // Ordenar por fecha descendente
            limit: 5                               // Limitar a los primeros 5 registros
        },
    })
);
```

# Referencia a la docuemntacion
https://docs.directus.io/reference/authentication.html

