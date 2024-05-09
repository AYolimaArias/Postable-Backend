# Postable

This project presents a RESTful API that simulates a social network, allowing users to interact with posts. The API handles various operations based on user authentication.

## Table of Contents

1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [Endpoints](#endpoints)
5. [Request Examples](#request-examples)
6. [Authentication](#authentication)


## Requirements

You need to have Node.js, npm, and PostgreSQL installed in your development environment.

## Installation

1. Clone this repository:

```bash
git clone git@github.com:AYolimaArias/Postable-Backend.git
cd Postable-Backend
```

2. Install dependencies:

```bash
npm install
```

3. Configure the database connection in the .env file, an example is provided in the .env.example file.

4. Migrate the database:
   
```bash
npm run db:reset
npm run db:seed
```

5. Inicia el servidor:

```bash
npm run dev
```

## Configuration

To properly configure your development environment, you'll need to create a .env file in the root of the project and set the following environment variables.

The .env.example file provides a template for users to copy and configure the variables according to their needs, similar to the following example:

```plaintext
# File .env content

# PostgreSQL database configuraton
DB_HOST=localhost
DB_NAME=mi_base_de_datos
DB_PORT=5432
DB_USER=mi_usuario
DB_PASSWORD=mi_contraseña
DB_ADMIN_DATABASE=mi_database_admin

# Token JWT configuraton
JWT_SECRET=mi_secreto
COST_FACTOR=10
```

Make sure to provide specific values for each variable according to your application's requirements.

### Example Database Migration:

You can directly migrate the example database using the following commands:

- Create database:
  
```bash
npm run db:create 
```
- Create tables:

```bash
npm run db:migrate up
```
- Migrate information from users and :

```bash
npm run db:seed
```
- Borrar y volver a crear base de datos y tablas con un solo comando:

```bash
npm run db:reset
```

## Estructura del proyecto

La aplicación sigue una arquitectura de tres capas:

- **Routers:** Define las rutas y maneja las solicitudes HTTP.
- **Servicios:** Contiene la lógica de negocio y se comunica con la capa de acceso a datos.
- **Acceso a Datos:** Gestiona las interacciones con la base de datos PostgreSQL utilizando pg.

## Endpoints

### Visualización de Posts

#### GET / (Ver Todos los Posts con Paginación y Filtros)

- **Descripción**: Retorna una lista paginada de posts con opciones de filtrado y ordenación.
- **Parámetros Query**: Permite paginación, filtrado por usuario, y ordenación por fecha o likes.

#### GET /:username (Ver Posts de Usuario Específico)

- **Descripción**: Muestra los posts de un usuario específico con paginación y ordenación.
- **Parámetros**: `username` - Usuario a consultar.
- **Parámetros Query**: Permite paginación y ordenación por fecha o likes.

### Interacción de Usuarios Registrados

#### POST /posts (Crear Nuevo Post)

- **Descripción**: Permite a un usuario registrado crear un nuevo post.
- **Body**: `content` - Texto del post.
- **Respuesta**: Crea un nuevo post y devuelve la información del post creado.

#### PATCH /posts/:id (Editar Post Existente)

- **Descripción**: Permite a un usuario registrado editar un post existente.
- **Parámetros URL**: `id` - ID del post a editar.
- **Body**: `content` - Texto actualizado del post (opcional).
- **Respuesta**: Actualiza el post y devuelve la información actualizada.

#### POST /posts/:postId/like (Dar Like a un Post)

- **Descripción**: Permite a un usuario registrado dar "Like" a un post.
- **Parámetros**: `postId` - ID del post a dar like.
- **Respuesta**: Registra un "Like" al post y devuelve la información actualizada del post.

#### DELETE /posts/:postId/like (Eliminar Like de un Post)

- **Descripción**: Permite a un usuario eliminar su "Like" de un post.
- **Parámetros**: `postId` - ID del post a remover like.
- **Respuesta**: Elimina el "Like" del post y devuelve la información actualizada del post.

### Registro y Autenticación de Usuarios

#### POST /signup (Crear Cuenta)

- **Descripción**: Permite a un nuevo usuario registrarse en la plataforma.
- **Body**: `username`, `password` - Campos requeridos para el registro.
- **Respuesta**: Crea una nueva cuenta y devuelve la información del usuario registrado.

#### POST /login (Iniciar Sesión)

- **Descripción**: Permite a un usuario existente iniciar sesión.
- **Body**: `username`, `password` - Credenciales requeridas para el inicio de sesión.
- **Respuesta**: Inicia sesión y devuelve un token JWT.

### Gestión de Perfil de Usuario

#### GET /me (Ver Perfil de Usuario)

- **Descripción**: Muestra el perfil del usuario autenticado.
- **Respuesta**: Devuelve la información del perfil en formato JSON.

#### PATCH /me (Editar Cuenta de Usuario)

- **Descripción**: Permite al usuario editar su información de perfil.
- **Body**: Permite actualizar campos opcionales como email, firstName, lastName.
- **Respuesta**: Actualiza

## Ejemplos de solicitudes

#### Visualización de Posts

##### GET / (Ver Todos los Posts con Paginación y Filtros)

- **Descripción**: Retorna una lista de posts disponibles en la plataforma, con opciones de paginación, filtrado por usuario y ordenación.
- **Parámetros Query**:
  - `page`: Número de página (opcional, por defecto 1).
  - `limit`: Número de posts por página (opcional, por defecto 10).
  - `username`: Filtrar posts por nombre de usuario (opcional).
  - `orderBy`: Criterio de ordenación, opciones: createdAt, likesCount (opcional, por defecto createdAt).
  - `order`: Dirección de la ordenación, opciones: asc, desc (opcional, por defecto asc).
- **Respuesta**:
  - `200 OK`: Lista paginada de posts en formato JSON.
  - **Ejemplo de Respuesta**:
    ```json
    {
      "ok": true,
      "data": [
        {
          "id": 1,
          "content": "Este es un post",
          "createdAt": "2024-01-19 07:37:16-08",
          "updatedAt": "2024-01-19 07:37:16-08",
          "username": "usuario1",
          "likesCount": 5
        },
        ...
      ],
      "pagination": {
        "page": 1,
        "pageSize": 10,
        "totalItems": 20,
        "totalPages": 2,
        "nextPage": 2,
        "previousPage": null
      }
    }
    ```
  - **Ejemplo de Uso**:
    Para obtener la segunda página de posts, limitando a 5 posts por página, filtrando por el usuario 'usuarioEjemplo', ordenados por número de likes en orden descendente:
    `GET /?page=2&limit=5&username=usuarioEjemplo&orderBy=likesCount&order=desc`

##### GET /:username (Ver Posts de Usuario Específico)

- **Descripción**: Muestra los posts de un usuario específico con opciones de paginación y ordenación.
- **Parámetros**:
  - `username`: Usuario a consultar.
- **Parámetros Query**:
  - `page`: Número de página (opcional, por defecto 1).
  - `limit`: Número de posts por página (opcional, por defecto 10).
  - `orderBy`: Criterio de ordenación, opciones: createdAt, likesCount (opcional, por defecto createdAt).
  - `order`: Dirección de la ordenación, opciones: asc, desc (opcional, por defecto asc).
- **Respuesta**:
  - `200 OK`: Lista de posts del usuario en formato JSON.
  - `404 Not Found`: Si el usuario no existe.
  - **Ejemplo de Respuesta**:
    ```json
    {
      "ok": true,
      "data": [
        {
          "id": 2,
          "content": "Post del usuario",
          "createdAt": "2024-01-19 05:37:16-08",
          "updatedAt": "2024-01-19 05:37:16-08",
          "username": "usuario-específico",
          "likesCount": 0
        },
        ...
      ],
      "pagination": {
        "page": 1,
        "pageSize": 10,
        "totalItems": 5,
        "totalPages": 1,
        "nextPage": null,
        "previousPage": null
      }
    }
    ```

#### Interacción de Usuarios Registrados

##### POST /posts (Crear Nuevo Post)

- **Descripción**: Permite a un usuario registrado crear un nuevo post.
- **Body**:
  - `content`: Texto del post.
- **Respuesta**:
  - `201 Created`: Post creado exitosamente.
  - `400 Bad Request`: Si falta información o el formato es incorrecto.
  - `401 Unauthorized`: Si el usuario no está autenticado.
  - **Ejemplo de Respuesta**:
    ```json
    {
      "ok": true,
      "data": {
        "id": 10,
        "content": "Mi nuevo post",
        "createdAt": "2024-01-19 10:37:16-08",
        "updatedAt": "2024-01-19 10:37:16-08",
        "username": "mi-usuario",
        "likesCount": 0
      }
    }
    ```

##### PATCH /posts/:id (Editar Post Existente)

- **Descripción**: Permite a un usuario registrado editar un post existente.
- **Parámetros URL**:
  - `id`: ID del post a editar.
- **Body**:
  - `content`: Texto actualizado del post. (El campo es opcional, pero se debe enviar al menos un campo para actualizar)
- **Respuesta**:
  - `200 OK`: Post actualizado exitosamente. Devuelve el post actualizado.
  - `400 Bad Request`: Si falta información, el formato es incorrecto o no se envía ningún campo para actualizar.
  - `401 Unauthorized`: Si el usuario no está autenticado o no es el propietario del post.
  - `404 Not Found`: Si el post no existe.
  - **Ejemplo de Respuesta**:
    ```json
    {
      "ok": true,
      "data": {
        "id": 10,
        "content": "Mi post actualizado",
        "createdAt": "2024-01-19 10:37:16-08",
        "updatedAt": "2024-01-19 11:00:00-08",
        "username": "mi-usuario",
        "likesCount": 0
      }
    }
    ```

##### POST /posts/:postId/like (Dar Like a un Post)

- **Descripción**: Permite a un usuario registrado dar "Like" a un post.
- **Parámetros**:
  - `postId`: ID del post a dar like.
- **Respuesta**:
  - `200 OK`: Like registrado.
  - `404 Not Found`: Si el post no existe.
  - `401 Unauthorized`: Si el usuario no está autenticado.
  - **Ejemplo de Respuesta**:
    ```json
    {
      "ok": true,
      "data": {
        "id": 15,
        "content": "Mi nuevo post",
        "createdAt": "2024-01-19 10:37:16-08",
        "updatedAt": "2024-01-19 10:37:16-08",
        "username": "usuario",
        "likesCount": 1
      }
    }
    ```

##### DELETE /posts/:postId/like (Eliminar Like de un Post)

- **Descripción**: Permite a un usuario eliminar su "Like" de un post.
- **Parámetros**:
  - `postId`: ID del post a remover like.
- **Respuesta**:
  - `200 OK`: Like eliminado.
  - `404 Not Found`: Si el post no existe o no tenía like previamente.
  - `401 Unauthorized`: Si el usuario no está autenticado.
  - **Ejemplo de Respuesta**:
    ```json
    {
      "ok": true,
      "data": {
        "id": 15,
        "content": "Mi nuevo post",
        "createdAt": "2024-01-19 10:37:16-08",
        "updatedAt": "2024-01-19 10:37:16-08",
        "username": "usuario",
        "likesCount": 0
      }
    }
    ```

#### Registro y Autenticación de Usuarios

##### POST /signup (Crear Cuenta)

- **Descripción**: Permite a un nuevo usuario registrarse en la plataforma.
- **Body**:
  - `username`, `password`: Campos requeridos para el registro.
- **Respuesta**:
  - `201 Created`: Cuenta creada.
  - `400 Bad Request`: Si falta información o el formato es incorrecto.
  - **Ejemplo de Respuesta**:
    ```json
    {
      "ok": true,
      "data": {
        "id": 20,
        "username": "nuevoUsuario",
        "email": "un-mail@example.com",
        "firstName": "Nombre",
        "lastName": "Apellido",
        "createdAt": "2024-01-19 10:37:16-08",
        "updatedAt": "2024-01-19 10:37:16-08"
      }
    }
    ```

##### POST /login (Iniciar Sesión)

- **Descripción**: Permite a un usuario existente iniciar sesión.
- **Body**:
  - `username`, `password`: Credenciales requeridas para el inicio de sesión.
- **Respuesta**:
  - `200 OK`: Sesión iniciada, retorna token JWT.
  - `401 Unauthorized`: Credenciales incorrectas.
  - **Ejemplo de Respuesta**:
    ```json
    {
      "ok": true,
      "data": {
        "token": "eyJhbGciOiJIUzI1NiIsInR5..."
      }
    }
    ```

#### Gestión de Perfil de Usuario

##### GET /me (Ver Perfil de Usuario)

**Descripción:** Muestra el perfil del usuario autenticado.

**Respuesta:**

- 200 OK: Información del perfil en formato JSON.
- 401 Unauthorized: Si el usuario no está autenticado.

**Ejemplo de Respuesta:**

```json
{
  "ok": true,
  "data": {
    "id": 2,
    "username": "miUsuario",
    "email": "miemail@example.com",
    "firstName": "Nombre",
    "lastName": "Apellido",
    "createdAt": "2024-01-19 10:37:16-08",
    "updatedAt": "2024-01-19 10:37:16-08"
  }
}
```

###### PATCH /me (Editar Cuenta de Usuario)

**Descripción:** Permite al usuario editar su información de perfil.

**Body:**

- email, firstName, lastName: Campos opcionales para actualizar.

**Respuesta:**

- 200 OK: Perfil actualizado.
- 400 Bad Request: Si el formato es incorrecto.
- 401 Unauthorized: Si el usuario no está autenticado.

**Ejemplo de Respuesta:**

```json
{
  "ok": true,
  "data": {
    "id": 2,
    "username": "miUsuario",
    "email": "nuevo@mail.com",
    "firstName": "Nombre",
    "lastName": "Apellido",
    "createdAt": "2024-01-19 10:37:16-08",
    "updatedAt": "2024-01-19 11:00:16-08"
  }
}
```

##### DELETE /me (Eliminar Cuenta de Usuario)

**Descripción:** Permite al usuario eliminar su cuenta.

**Respuesta:**

- 200 OK: Cuenta eliminada.
- 401 Unauthorized: Si el usuario no está autenticado.

**Ejemplo de Respuesta:**

```json
{
  "ok": true
}
```

## Autenticación

En este proyecto, utilizamos JSON Web Token (JWT) para gestionar la autenticación. JWT es un estándar abierto (RFC 7519) que define una forma compacta y autónoma de transmitir información de manera segura entre las partes como un objeto JSON. En el contexto de la autenticación, JWT se utiliza para generar tokens que pueden ser verificados para asegurar la identidad del usuario.

### Cómo Funciona

1. **Generación del Token:**

   - Cuando un usuario se autentica con éxito, se genera un JWT que contiene información sobre el usuario y posiblemente otros datos relevantes.

2. **Envío del Token:**

   - El token JWT se envía al cliente, generalmente como parte de la respuesta después de una autenticación exitosa.

3. **Almacenamiento del Token:**

   - El cliente almacena el token, generalmente en el almacenamiento local o en las cookies, para incluirlo en las solicitudes posteriores a recursos protegidos.

4. **Verificación del Token:**
   - En cada solicitud a un recurso protegido, el servidor verifica la validez del token JWT recibido. Si el token es válido, se permite el acceso al recurso protegido.

