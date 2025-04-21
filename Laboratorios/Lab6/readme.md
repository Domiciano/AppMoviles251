# Laboratorio 6

El propósito del laboratorio es instalar su primera versión de base de datos en postgres, de modo que puedan ser usadas desde directus.

Use su esquema de base de datos construido *de momento* para generar un archivo `database.sql`. Usando este archivo generaremos las tablas necesarias del modelo.

Los scripts de SQL se ejecutan linea por linea. 

Puede incluir creaciones de tabla (CREATE TABLE), también pueden incluir inserciones de datos (INSERT INTO).

Directus leerá esto automáticamente y simplemente tendrá que ir a la consola de administrador para hablitar los esquemas.

**Use las contraseñas de administrador para esta práctica**

Puede ayudarse de https://sqliteonline.com/

# Creando tablas simples
Si requiere construir tablas que no tienen relación con ninguna otra tabla de su base de datos puede usar
```sql
CREATE TABLE game_item_shop (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

En este caso dado que `created_at` tiene un valor por defecto, no es necesario nombrarlo al hacer la inserciones.

```sql
INSERT INTO game_item_shop (name, description, price)
VALUES (
    'Espada de Fuego',
    'Una espada legendaria que inflige daño de fuego adicional a los enemigos.',
    149.99
);
```

# Relaciones 1 a muchos

Si requiere establecer una relación 1 a muchos use

```sql
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    title TEXT NOT NULL,
    content TEXT,
    publication_date TIMESTAMP
);

CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    post_id INTEGER REFERENCES posts(id) ON DELETE CASCADE,
    author TEXT NOT NULL,
    text TEXT NOT NULL,
    creation_date TIMESTAMP
);
```

Una vez en directus, los timestamp se envian por medio del formato `2025-04-20T10:00:00`. También recibe el formato `2025-04-20`

Para las inserciones puede usar

```sql
INSERT INTO posts (title, content, publication_date)
VALUES (
  'Understanding SQL Relationships',
  'This post explains one-to-many relationships with examples.',
  '2025-04-20 10:00:00'
);

INSERT INTO comments (post_id, author, text, creation_date)
VALUES 
  (1, 'Alice', 'Great explanation, very helpful!', '2025-04-20 11:00:00'),
  (1, 'Bob', 'I was confused about this before, thanks!', '2025-04-20 11:30:00');
```

# Relaciones muchos a muchos

Suponga que requiere hacer una relación muchos a muchos. En este caso debe tener sus dos tablas principales

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    price DECIMAL(10, 2) NOT NULL
);

CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL
);
```

Luego genere la tabla intermedia con las referencias
```sql
CREATE TABLE products_categories (
    id SERIAL PRIMARY KEY,
    product_id INTEGER REFERENCES products(id) ON DELETE CASCADE,
    category_id INTEGER REFERENCES categories(id) ON DELETE CASCADE
);
```

Puede agregar datos asi

```sql
-- Insertar productos
INSERT INTO products (name, price) VALUES
('Camiseta Básica', 20000.00),
('Zapatos Casual', 120000.00),
('Mochila Escolar', 85000.00),
('Gorra Deportiva', 35000.00),
('Audífonos Bluetooth', 150000.00);

-- Insertar categorías
INSERT INTO categories (name) VALUES
('Ropa'),
('Calzado'),
('Accesorios'),
('Tecnología');

-- Insertar asociaciones entre productos y categorías
INSERT INTO products_categories (product_id, category_id) VALUES
(1, 1),  -- Camiseta Básica - Ropa
(2, 2),  -- Zapatos Casual - Calzado
(3, 1),  -- Mochila Escolar - Ropa
(4, 3),  -- Gorra Deportiva - Accesorios
(5, 4);  -- Audífonos Bluetooth - Tecnología
```

# Referenciando tablas de directus

Puede también construir tablas que se relaciones con las directus. Quizás la única que necesite sea `directus_users` para generar su propia tabla de `user_profile`. 

Para ese caso

```sql
CREATE TABLE user_profile (
    id SERIAL PRIMARY KEY,
    user_id UUID UNIQUE REFERENCES directus_users(id) ON DELETE CASCADE,
    address TEXT,
    phone TEXT,
    birthdate DATE
);
```

Si ya creó la tabla de perfiles, puede crear perfiles para cada usuario asi

```sql
-- Insertar perfiles
INSERT INTO user_profile (user_id, address, phone, birthdate)
VALUES
  ((SELECT id FROM directus_users WHERE email = 'carlos@tienda.com'), 'Calle Ficticia 123, Ciudad', '555-1234', '1990-01-01'),
  ((SELECT id FROM directus_users WHERE email = 'sandra@tienda.com'), 'Avenida Inventada 456, Ciudad', '555-5678', '1985-02-15');
```

# Creando roles iniciales

```sql
INSERT INTO directus_roles (id, name, icon, description, parent) VALUES
  ('11111111-1111-1111-1111-111111111111', 'Comprador', 'supervised_user_circle', 'Usuario con permisos para comprar productos', NULL),
  ('22222222-2222-2222-2222-222222222222', 'Vendedor', 'supervised_user_circle', 'Usuario con permisos para vender productos', NULL);
```


# Generar usuarios de prueba
```sql
-- Insertar usuarios de prueba
INSERT INTO directus_users (id, first_name, email, password, role, status)
VALUES
  (gen_random_uuid(), 'Carlos Comprador', 'carlos@tienda.com', '$argon2i$v=19$m=16,t=2,p=1$Qk4zb1RuWDJCcVRpb2JoSw$SzyC3tGEP6swGBt0TvBkiw', 
    (SELECT id FROM directus_roles WHERE name = 'Comprador'), 'active'),
  (gen_random_uuid(), 'Sandra Vendedora', 'sandra@tienda.com', '$argon2i$v=19$m=16,t=2,p=1$Qk4zb1RuWDJCcVRpb2JoSw$SzyC3tGEP6swGBt0TvBkiw', 
    (SELECT id FROM directus_roles WHERE name = 'Vendedor'), 'active');
```
Donde `QfTIfUcdw0EVx5mKx6OqLONtdVx1hdp6Q5RQlQYQdMN6Q4nksf22i` es `12345678` hasheado con **[Argon2](https://argon2.online/)**

Si tiene certeza de sus UUID también puede usar
```sql
INSERT INTO directus_users (id, first_name, email, password, role, status)
VALUES
  (gen_random_uuid(), 'Carlos Comprador', 'carlos@tienda.com', '$argon2i$v=19$m=16,t=2,p=1$Qk4zb1RuWDJCcVRpb2JoSw$SzyC3tGEP6swGBt0TvBkiw', 
    '11111111-1111-1111-1111-111111111111', 'active'),
  (gen_random_uuid(), 'Sandra Vendedora', 'sandra@tienda.com', '$argon2i$v=19$m=16,t=2,p=1$Qk4zb1RuWDJCcVRpb2JoSw$SzyC3tGEP6swGBt0TvBkiw', 
    '22222222-2222-2222-2222-222222222222', 'active');
```



# Habilitar permisos de creación de usuario pública

```sql
INSERT INTO directus_permissions (
    collection,
    action,
    fields,
    policy,
    permissions,
    validation
) VALUES (
    'directus_users',
    'create',
    '*',
    (SELECT id FROM directus_policies WHERE name LIKE '%public_label%'),
    '{}',
    '{}'
);
```

Además requerirá acceso público a los roles de la aplicación

```sql
INSERT INTO directus_permissions (
    collection,
    action,
    fields,
    policy,
    permissions,
    validation
) VALUES (
    'directus_roles',
    'read',
    '*',
    (SELECT id FROM directus_policies WHERE name LIKE '%public_label%'),
    '{}',
    '{}'
);
```


# Queries 


### Por ID

Puede obtener un objeto por ID

```http
GET /items/post/11
```

### Filtrado

Puede obtener un objeto por ID
Suponga que quiere buscar por un campo especifico. En el ejemplo que el titulo sea Aplicaciones 

```http
GET /items/post?filter[title][_eq]=Aplicaciones
```

Pero tambien puede ser un contains

```http
GET /items/post?filter[title][_icontains]=<String de búsqueda>
```

También puede filtrar por cualquier coincidencia en cualquier campo

```http
GET /items/post?search=fa
```

### Control de campos
Puede elegir qué campos requiere. Por ejemplo, si sólo nos interesa el `title` y el `body`

```http
GET http://localhost:8055/items/post?fields=title,body
```

### Pagination

En caso de que no requiera todos los elementos de base de datos. Use

```http
GET /items/post?limit=5&offset=0
```

Donde está obteniendo los 5 elementos, desde el principio

### Ordenamiento

Puede también ordenar por cualquier campo

```http
GET /items/post?sort=-title
```
El signo `-` implica que entregará los resultados de forma descendente. Sin el signo, entregará el orden de forma ascendente.


# Queries compuestas

```
http://localhost:8055/items/comentarios?fields=id,autor,texto,publicacion_id.titulo,publicacion_id.contenido
```

https://directus.io/docs/api/items

# ANEXOS

Este es mi `data.sql` con el que inicializo mi aplicación

```sql
-- Creamos los roles con un UUID especifico
INSERT INTO directus_roles (id, name, icon, description, parent) VALUES
  ('11111111-1111-1111-1111-111111111111', 'Comprador', 'supervised_user_circle', 'Usuario con permisos para comprar productos', NULL),
  ('22222222-2222-2222-2222-222222222222', 'Vendedor', 'supervised_user_circle', 'Usuario con permisos para vender productos', NULL);


-- Creamos los usuarios para que puedan iniciar sesion
INSERT INTO directus_users (id, first_name, email, password, role, status)
VALUES
  (gen_random_uuid(), 'Carlos Comprador', 'carlos@tienda.com', '$argon2i$v=19$m=16,t=2,p=1$Qk4zb1RuWDJCcVRpb2JoSw$SzyC3tGEP6swGBt0TvBkiw', 
    '11111111-1111-1111-1111-111111111111', 'active'),
  (gen_random_uuid(), 'Sandra Vendedora', 'sandra@tienda.com', '$argon2i$v=19$m=16,t=2,p=1$Qk4zb1RuWDJCcVRpb2JoSw$SzyC3tGEP6swGBt0TvBkiw', 
    '22222222-2222-2222-2222-222222222222', 'active');

-- Hacemos que la creacion sea publica
INSERT INTO directus_permissions (
    collection,
    action,
    fields,
    policy,
    permissions,
    validation
) VALUES (
    'directus_users',
    'create',
    '*',
    (SELECT id FROM directus_policies WHERE name LIKE '%public_label%'),
    '{}',
    '{}'
);

-- Hacemos publico el acceso a listar los roles
INSERT INTO directus_permissions (
    collection,
    action,
    fields,
    policy,
    permissions,
    validation
) VALUES (
    'directus_roles',
    'read',
    '*',
    (SELECT id FROM directus_policies WHERE name LIKE '%public_label%'),
    '{}',
    '{}'
);

-- Aqui iria el esquema segun las reglas de negocio

CREATE TABLE user_profile (
    id SERIAL PRIMARY KEY,
    user_id UUID UNIQUE REFERENCES directus_users(id) ON DELETE CASCADE,
    address TEXT,
    phone TEXT,
    birthdate DATE
);

INSERT INTO user_profile (user_id, address, phone, birthdate)
VALUES
  ((SELECT id FROM directus_users WHERE email = 'carlos@tienda.com'), 'Calle Ficticia 123, Ciudad', '555-1234', '1990-01-01'),
  ((SELECT id FROM directus_users WHERE email = 'sandra@tienda.com'), 'Avenida Inventada 456, Ciudad', '555-5678', '1985-02-15');
```

Y para ejecutarlo tengo este archivo `.sh`

```sh
#!/bin/bash

# Nombre del contenedor
CONTAINER_NAME="directusmoviles_db_1"

# Ruta al archivo SQL
SQL_FILE="./data.sql"

# Nombre de la base de datos y usuario
DB_NAME="directus"
DB_USER="directus"

# Copiar el archivo SQL al contenedor
docker cp "$SQL_FILE" "$CONTAINER_NAME":/tmp/data.sql

# Ejecutar el SQL dentro del contenedor
docker exec -i "$CONTAINER_NAME" psql -U "$DB_USER" -d "$DB_NAME" -f /tmp/data.sql -w
```

o en Windows

```bat
@echo off

SET CONTAINER_NAME=directusmoviles_db_1
SET SQL_FILE=.\data.sql
SET DB_NAME=directus
SET DB_USER=directus

docker cp "%SQL_FILE%" "%CONTAINER_NAME%:/tmp/data.sql"
docker exec -i %CONTAINER_NAME% psql -U %DB_USER% -d %DB_NAME% -f /tmp/data.sql -w

pause
```
