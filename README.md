# Procedures CafeLab - API Reference

## Overview

- Microservicio: `Procedures-CafeLab`
- Base path real: `/api/v1`
- Modulos documentados: `Recipes`, `Portfolios`, `Ingredients` y `Cupping Sessions`
- Formato de respuestas: `application/json`
- Autenticacion: en la implementacion actual **todos los endpoints REST documentados son publicos** (`permitAll` en `SecurityConfiguration`). El `userId` se envia en el cuerpo o en la ruta segun el caso; los comentarios Swagger mencionan JWT, pero no hay filtro JWT activo en este servicio.
- Swagger UI disponible en: `/swagger-ui/index.html`
- OpenAPI JSON disponible en: `/v3/api-docs`

## Reglas Generales

- Los endpoints bajo `/api/v1/recipes/**`, `/api/v1/portfolios/**`, `/api/v1/cupping-sessions/**` no exigen cabecera `Authorization` en la configuracion actual.
- Los endpoints con body esperan `Content-Type: application/json`.
- Los errores controlados suelen responder con `{"message":"..."}` (`MessageResource`).
- Las validaciones de los records Java lanzan `IllegalArgumentException` y el controller responde **HTTP 400** con `message`.
- Los recursos no encontrados en Preparation y Cupping Sessions responden **HTTP 404** con `message` fijo por tipo de entidad.
- `GET /api/v1/cupping-sessions/{userId}` lista por usuario usando solo el path param (sin prefijo `user/`).
- `GET`, `PUT` y `DELETE` de cupping por id usan la ruta `/api/v1/cupping-sessions/user/{userId}/{sessionId}`.
- `DELETE /api/v1/cupping-sessions/user/{userId}/{sessionId}` responde **HTTP 204** sin cuerpo.
- `DELETE` de recetas, portafolios e ingredientes responde **HTTP 200** con `{"message":"..."}`.
- `createdAt` en recetas y portafolios se serializa como `string` (`Instant.toString()`).
- Fechas (`sessionDate`) usan formato ISO-8601 de fecha: `YYYY-MM-DD`.

## Indice de Endpoints

| Modulo | Operacion | Metodo | Ruta | Auth |
| --- | --- | --- | --- | --- |
| Recipes | Crear receta | `POST` | `/api/v1/recipes` | Publico |
| Recipes | Listar recetas por usuario | `GET` | `/api/v1/recipes/user/{userId}` | Publico |
| Recipes | Obtener receta por id | `GET` | `/api/v1/recipes/user/{userId}/{recipeId}` | Publico |
| Recipes | Actualizar receta | `PUT` | `/api/v1/recipes/user/{userId}/{recipeId}` | Publico |
| Recipes | Eliminar receta | `DELETE` | `/api/v1/recipes/user/{userId}/{recipeId}` | Publico |
| Portfolios | Crear portafolio | `POST` | `/api/v1/portfolios` | Publico |
| Portfolios | Listar portafolios por usuario | `GET` | `/api/v1/portfolios/user/{userId}` | Publico |
| Portfolios | Obtener portafolio por id | `GET` | `/api/v1/portfolios/user/{userId}/{portfolioId}` | Publico |
| Portfolios | Actualizar portafolio | `PUT` | `/api/v1/portfolios/user/{userId}/{portfolioId}` | Publico |
| Portfolios | Eliminar portafolio | `DELETE` | `/api/v1/portfolios/user/{userId}/{portfolioId}` | Publico |
| Ingredients | Anadir ingrediente | `POST` | `/api/v1/recipes/{recipeId}/ingredients` | Publico |
| Ingredients | Listar ingredientes | `GET` | `/api/v1/recipes/{recipeId}/ingredients` | Publico |
| Ingredients | Actualizar ingrediente | `PUT` | `/api/v1/recipes/{recipeId}/ingredients/{ingredientId}` | Publico |
| Ingredients | Eliminar ingrediente | `DELETE` | `/api/v1/recipes/{recipeId}/ingredients/{ingredientId}` | Publico |
| Cupping Sessions | Crear sesion de cata | `POST` | `/api/v1/cupping-sessions` | Publico |
| Cupping Sessions | Listar sesiones por usuario | `GET` | `/api/v1/cupping-sessions/{userId}` | Publico |
| Cupping Sessions | Obtener sesion por id | `GET` | `/api/v1/cupping-sessions/user/{userId}/{sessionId}` | Publico |
| Cupping Sessions | Actualizar sesion | `PUT` | `/api/v1/cupping-sessions/user/{userId}/{sessionId}` | Publico |
| Cupping Sessions | Eliminar sesion | `DELETE` | `/api/v1/cupping-sessions/user/{userId}/{sessionId}` | Publico |

---

<h2 style="color:#0A58CA;">Recipes - Crear receta</h2>

<p>
<span style="background:#198754;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">POST</span>
<code>/api/v1/recipes</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Content-Type` | Requerido. Usar `application/json`. |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (body)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. Debe ser positivo. Identificador del perfil propietario. |
| `name` | `string` | Requerido. Nombre de la receta. |
| `imageUrl` | `string` | Requerido. URL o referencia de imagen. |
| `extractionMethod` | `string` | Requerido. Metodo de extraccion (ej. `v60`). |
| `extractionCategory` | `string` | Requerido. Categoria de extraccion (ej. `coffee`). |
| `ratio` | `string` | Requerido. Relacion agua/cafe (ej. `1:16`). |
| `cuppingSessionId` | `number` | `Opcional`. Sesion de cata asociada. |
| `portfolioId` | `number` | `Opcional`. Portafolio asociado. |
| `preparationTime` | `number` | Requerido. Tiempo en segundos; no puede ser negativo. |
| `steps` | `string` | Requerido. Pasos de preparacion. |
| `tips` | `string` | `Opcional`. Si se omite, queda `""`. |
| `cupping` | `string` | `Opcional`. Si se omite, queda `""`. |
| `grindSize` | `string` | `Opcional`. Si se omite o esta vacio, queda `"-"`. |

### Request Example

```json
{
  "userId": 9401,
  "name": "Receta funcional",
  "imageUrl": "img",
  "extractionMethod": "v60",
  "extractionCategory": "coffee",
  "ratio": "1:16",
  "cuppingSessionId": null,
  "portfolioId": null,
  "preparationTime": 180,
  "steps": "Pasos",
  "tips": "Tips",
  "cupping": "ok",
  "grindSize": "medio"
}
```

### Success Response

**HTTP 201**

```json
{
  "id": 1,
  "userId": 9401,
  "name": "Receta funcional",
  "imageUrl": "img",
  "extractionMethod": "v60",
  "extractionCategory": "coffee",
  "ratio": "1:16",
  "cuppingSessionId": null,
  "portfolioId": null,
  "preparationTime": 180,
  "steps": "Pasos",
  "tips": "Tips",
  "cupping": "ok",
  "grindSize": "medio",
  "createdAt": "2026-06-01T12:00:00Z",
  "ingredients": []
}
```

### Default Response

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `id` | `number` | Identificador de la receta creada. |
| `userId` | `number` | Propietario de la receta. |
| `name` | `string` | Nombre de la receta. |
| `imageUrl` | `string` | Imagen de la receta. |
| `extractionMethod` | `string` | Metodo de extraccion. |
| `extractionCategory` | `string` | Categoria de extraccion. |
| `ratio` | `string` | Relacion de preparacion. |
| `cuppingSessionId` | `number` | Sesion de cata vinculada, si existe. |
| `portfolioId` | `number` | Portafolio vinculado, si existe. |
| `preparationTime` | `number` | Tiempo de preparacion en segundos. |
| `steps` | `string` | Pasos de la receta. |
| `tips` | `string` | Consejos. |
| `cupping` | `string` | Notas de cata. |
| `grindSize` | `string` | Tamano de molienda. |
| `createdAt` | `string` | Marca de tiempo de creacion. |
| `ingredients` | `array` | Lista de ingredientes (puede venir vacia al crear). |

### Extra Response

No aplica.

### Error 4xx

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `message` | `string` | Mensaje simple de error. |

Error 400 por validacion del record:

```json
{
  "message": "Name es requerido"
}
```

Error 400 si no se pudo persistir:

```json
{
  "message": "No se pudo crear la receta. Intente de nuevo."
}
```

---

<h2 style="color:#0A58CA;">Recipes - Listar recetas por usuario</h2>

<p>
<span style="background:#0A58CA;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">GET</span>
<code>/api/v1/recipes/user/{userId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. Identificador del usuario cuyas recetas se listan. |

### Request Example

```http
GET /api/v1/recipes/user/9401
Accept: application/json
```

### Success Response

**HTTP 200**

```json
[
  {
    "id": 1,
    "userId": 9401,
    "name": "Receta funcional",
    "imageUrl": "img",
    "extractionMethod": "v60",
    "extractionCategory": "coffee",
    "ratio": "1:16",
    "cuppingSessionId": null,
    "portfolioId": null,
    "preparationTime": 180,
    "steps": "Pasos",
    "tips": "Tips",
    "cupping": "ok",
    "grindSize": "medio",
    "createdAt": "2026-06-01T12:00:00Z",
    "ingredients": []
  }
]
```

### Default Response

Arreglo de objetos `RecipeResource` con la misma forma que en crear receta.

### Extra Response

No aplica.

### Error 4xx

No hay errores especificos documentados en el controller para listado vacio; devuelve `[]`.

---

<h2 style="color:#0A58CA;">Recipes - Obtener receta por id</h2>

<p>
<span style="background:#0A58CA;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">GET</span>
<code>/api/v1/recipes/user/{userId}/{recipeId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. Usuario propietario esperado. |
| `recipeId` | `number` | Requerido. Identificador de la receta. |

### Request Example

```http
GET /api/v1/recipes/user/9401/1
Accept: application/json
```

### Success Response

**HTTP 200**

Objeto `RecipeResource` (misma forma que en crear receta, con `ingredients` incluidos si existen).

### Default Response

Igual que **Recipes - Crear receta**.

### Extra Response

No aplica.

### Error 4xx

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `message` | `string` | Mensaje simple de error. |

Error 404:

```json
{
  "message": "Receta no encontrada"
}
```

---

<h2 style="color:#0A58CA;">Recipes - Actualizar receta</h2>

<p>
<span style="background:#6F42C1;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">PUT</span>
<code>/api/v1/recipes/user/{userId}/{recipeId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Content-Type` | Requerido. Usar `application/json`. |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. Usuario propietario. |
| `recipeId` | `number` | Requerido. Receta a actualizar. |

### Parametro (body)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `name` | `string` | Requerido. |
| `imageUrl` | `string` | Requerido. |
| `extractionMethod` | `string` | Requerido. |
| `extractionCategory` | `string` | Requerido. |
| `ratio` | `string` | Requerido. |
| `cuppingSessionId` | `number` | `Opcional`. |
| `portfolioId` | `number` | `Opcional`. |
| `preparationTime` | `number` | Requerido. No negativo. |
| `steps` | `string` | Requerido. |
| `tips` | `string` | `Opcional`. Default `""`. |
| `cupping` | `string` | `Opcional`. Default `""`. |
| `grindSize` | `string` | `Opcional`. Default `"-"` si vacio. |

### Request Example

```json
{
  "name": "Receta actualizada",
  "imageUrl": "img2",
  "extractionMethod": "v60",
  "extractionCategory": "coffee",
  "ratio": "1:15",
  "cuppingSessionId": null,
  "portfolioId": null,
  "preparationTime": 200,
  "steps": "Nuevos pasos",
  "tips": "",
  "cupping": "",
  "grindSize": "fino"
}
```

### Success Response

**HTTP 200**

Objeto `RecipeResource` actualizado.

### Default Response

Igual que **Recipes - Crear receta**.

### Extra Response

No aplica.

### Error 4xx

Error 400 por validacion:

```json
{
  "message": "Name es requerido"
}
```

Error 404:

```json
{
  "message": "Receta no encontrada"
}
```

---

<h2 style="color:#0A58CA;">Recipes - Eliminar receta</h2>

<p>
<span style="background:#DC3545;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">DELETE</span>
<code>/api/v1/recipes/user/{userId}/{recipeId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. Usuario propietario. |
| `recipeId` | `number` | Requerido. Receta a eliminar. |

### Request Example

```http
DELETE /api/v1/recipes/user/9401/1
Accept: application/json
```

### Success Response

**HTTP 200**

```json
{
  "message": "Receta eliminada exitosamente"
}
```

### Default Response

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `message` | `string` | Confirmacion de eliminacion. |

### Extra Response

No aplica.

### Error 4xx

Error 404:

```json
{
  "message": "Receta no encontrada"
}
```

---

<h2 style="color:#0A58CA;">Portfolios - Crear portafolio</h2>

<p>
<span style="background:#198754;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">POST</span>
<code>/api/v1/portfolios</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Content-Type` | Requerido. Usar `application/json`. |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (body)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. Debe ser positivo. |
| `name` | `string` | Requerido. Maximo 100 caracteres. |

### Request Example

```json
{
  "userId": 9402,
  "name": "Portafolio funcional"
}
```

### Success Response

**HTTP 201**

```json
{
  "id": 1,
  "userId": 9402,
  "name": "Portafolio funcional",
  "createdAt": "2026-06-01T12:00:00Z"
}
```

### Default Response

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `id` | `number` | Identificador del portafolio. |
| `userId` | `number` | Propietario (columna `user_id` en BD). |
| `name` | `string` | Nombre del portafolio. |
| `createdAt` | `string` | Marca de tiempo de creacion. |

### Extra Response

No aplica.

### Error 4xx

Error 400:

```json
{
  "message": "Name es requerido"
}
```

```json
{
  "message": "No se pudo crear el portafolio"
}
```

---

<h2 style="color:#0A58CA;">Portfolios - Listar portafolios por usuario</h2>

<p>
<span style="background:#0A58CA;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">GET</span>
<code>/api/v1/portfolios/user/{userId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. Usuario propietario. |

### Request Example

```http
GET /api/v1/portfolios/user/9402
Accept: application/json
```

### Success Response

**HTTP 200**

```json
[
  {
    "id": 1,
    "userId": 9402,
    "name": "Portafolio funcional",
    "createdAt": "2026-06-01T12:00:00Z"
  }
]
```

### Default Response

Arreglo de `PortfolioResource`.

### Extra Response

No aplica.

### Error 4xx

Listado vacio devuelve `[]`.

---

<h2 style="color:#0A58CA;">Portfolios - Obtener portafolio por id</h2>

<p>
<span style="background:#0A58CA;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">GET</span>
<code>/api/v1/portfolios/user/{userId}/{portfolioId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. |
| `portfolioId` | `number` | Requerido. |

### Request Example

```http
GET /api/v1/portfolios/user/9402/1
Accept: application/json
```

### Success Response

**HTTP 200**

Objeto `PortfolioResource`.

### Default Response

Igual que **Portfolios - Crear portafolio**.

### Extra Response

No aplica.

### Error 4xx

Error 404:

```json
{
  "message": "Portafolio no encontrado"
}
```

---

<h2 style="color:#0A58CA;">Portfolios - Actualizar portafolio</h2>

<p>
<span style="background:#6F42C1;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">PUT</span>
<code>/api/v1/portfolios/user/{userId}/{portfolioId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Content-Type` | Requerido. Usar `application/json`. |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. |
| `portfolioId` | `number` | Requerido. |

### Parametro (body)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `name` | `string` | Requerido. Maximo 100 caracteres. |

### Request Example

```json
{
  "name": "Portafolio renombrado"
}
```

### Success Response

**HTTP 200**

Objeto `PortfolioResource` actualizado.

### Default Response

Igual que **Portfolios - Crear portafolio**.

### Extra Response

No aplica.

### Error 4xx

Error 400:

```json
{
  "message": "Name es requerido"
}
```

Error 404 (respuesta del controller sin excepcion):

```json
{
  "message": "No se pudo actualizar el portafolio"
}
```

---

<h2 style="color:#0A58CA;">Portfolios - Eliminar portafolio</h2>

<p>
<span style="background:#DC3545;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">DELETE</span>
<code>/api/v1/portfolios/user/{userId}/{portfolioId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. |
| `portfolioId` | `number` | Requerido. |

### Request Example

```http
DELETE /api/v1/portfolios/user/9402/1
Accept: application/json
```

### Success Response

**HTTP 200**

```json
{
  "message": "Portafolio eliminado exitosamente"
}
```

### Default Response

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `message` | `string` | Confirmacion de eliminacion. |

### Extra Response

No aplica.

### Error 4xx

Error 404:

```json
{
  "message": "Portafolio no encontrado"
}
```

---

<h2 style="color:#0A58CA;">Ingredients - Anadir ingrediente</h2>

<p>
<span style="background:#198754;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">POST</span>
<code>/api/v1/recipes/{recipeId}/ingredients</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Content-Type` | Requerido. Usar `application/json`. |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `recipeId` | `number` | Requerido. Debe coincidir con `recipeId` del body. |

### Parametro (body)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `recipeId` | `number` | Requerido. Debe ser positivo e igual al de la ruta. |
| `name` | `string` | Requerido. Nombre del ingrediente. |
| `amount` | `number` | Requerido. Cantidad (puede ser cero en creacion). |
| `unit` | `string` | Requerido. Unidad (ej. `ml`, `g`). |

### Request Example

```json
{
  "recipeId": 1,
  "name": "Agua",
  "amount": 250.0,
  "unit": "ml"
}
```

### Success Response

**HTTP 201**

```json
{
  "id": 10,
  "recipeId": 1,
  "name": "Agua",
  "amount": 250.0,
  "unit": "ml"
}
```

### Default Response

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `id` | `number` | Identificador del ingrediente. |
| `recipeId` | `number` | Receta asociada. |
| `name` | `string` | Nombre del ingrediente. |
| `amount` | `number` | Cantidad. |
| `unit` | `string` | Unidad de medida. |

### Extra Response

No aplica.

### Error 4xx

Error 400 si `recipeId` de ruta y body no coinciden:

```json
{
  "message": "El ID de la receta no coincide con el de la ruta"
}
```

```json
{
  "message": "No se pudo crear el ingrediente"
}
```

---

<h2 style="color:#0A58CA;">Ingredients - Listar ingredientes</h2>

<p>
<span style="background:#0A58CA;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">GET</span>
<code>/api/v1/recipes/{recipeId}/ingredients</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `recipeId` | `number` | Requerido. Receta de la que se listan ingredientes. |

### Request Example

```http
GET /api/v1/recipes/1/ingredients
Accept: application/json
```

### Success Response

**HTTP 200**

```json
[
  {
    "id": 10,
    "recipeId": 1,
    "name": "Agua",
    "amount": 250.0,
    "unit": "ml"
  }
]
```

### Default Response

Arreglo de `IngredientResource`.

### Extra Response

No aplica.

### Error 4xx

No hay errores especificos; lista vacia devuelve `[]`.

---

<h2 style="color:#0A58CA;">Ingredients - Actualizar ingrediente</h2>

<p>
<span style="background:#6F42C1;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">PUT</span>
<code>/api/v1/recipes/{recipeId}/ingredients/{ingredientId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Content-Type` | Requerido. Usar `application/json`. |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `recipeId` | `number` | Requerido. Receta contenedora. |
| `ingredientId` | `number` | Requerido. Ingrediente a actualizar. |

### Parametro (body)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `name` | `string` | Requerido. |
| `amount` | `number` | Requerido. Debe ser positivo (`> 0`). |
| `unit` | `string` | Requerido. |

### Request Example

```json
{
  "name": "Agua filtrada",
  "amount": 300.0,
  "unit": "ml"
}
```

### Success Response

**HTTP 200**

Objeto `IngredientResource` actualizado.

### Default Response

Igual que **Ingredients - Anadir ingrediente**.

### Extra Response

No aplica.

### Error 4xx

Error 400:

```json
{
  "message": "Amount es requerido y debe ser positivo"
}
```

Error 404:

```json
{
  "message": "Ingrediente no encontrado"
}
```

---

<h2 style="color:#0A58CA;">Ingredients - Eliminar ingrediente</h2>

<p>
<span style="background:#DC3545;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">DELETE</span>
<code>/api/v1/recipes/{recipeId}/ingredients/{ingredientId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `recipeId` | `number` | Requerido. |
| `ingredientId` | `number` | Requerido. |

### Request Example

```http
DELETE /api/v1/recipes/1/ingredients/10
Accept: application/json
```

### Success Response

**HTTP 200**

```json
{
  "message": "Ingrediente eliminado exitosamente"
}
```

### Default Response

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `message` | `string` | Confirmacion de eliminacion. |

### Extra Response

No aplica.

### Error 4xx / 5xx

Error 403 si el ingrediente no pertenece a la receta:

```json
{
  "message": "Ingrediente no pertenece a esta receta"
}
```

Error 404:

```json
{
  "message": "Ingrediente no encontrado"
}
```

---

<h2 style="color:#0A58CA;">Cupping Sessions - Crear sesion de cata</h2>

<p>
<span style="background:#198754;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">POST</span>
<code>/api/v1/cupping-sessions</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Content-Type` | Requerido. Usar `application/json`. |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (body)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido en la practica. Identificador del perfil. |
| `name` | `string` | Requerido. |
| `origin` | `string` | Requerido. Origen del cafe. |
| `variety` | `string` | Requerido. Variedad. |
| `processing` | `string` | Requerido. Proceso. |
| `sessionDate` | `string` | Requerido. Fecha ISO `YYYY-MM-DD`. |
| `favorite` | `boolean` | `Opcional`. Si se omite o es `null`, queda `false`. |
| `resultsJson` | `string` | `Opcional`. JSON de resultados de cata. |
| `roastStyleNotes` | `string` | `Opcional`. Notas de tueste/estilo. |

### Request Example

```json
{
  "userId": 9301,
  "name": "Sesion funcional",
  "origin": "Peru",
  "variety": "Geisha",
  "processing": "Lavado",
  "sessionDate": "2026-05-05",
  "favorite": true,
  "resultsJson": "{}",
  "roastStyleNotes": "Notas funcionales"
}
```

### Success Response

**HTTP 201**

```json
{
  "id": 1,
  "userId": 9301,
  "name": "Sesion funcional",
  "origin": "Peru",
  "variety": "Geisha",
  "processing": "Lavado",
  "sessionDate": "2026-05-05",
  "favorite": true,
  "resultsJson": "{}",
  "roastStyleNotes": "Notas funcionales"
}
```

### Default Response

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `id` | `number` | Identificador de la sesion. |
| `userId` | `number` | Propietario. |
| `name` | `string` | Nombre de la sesion. |
| `origin` | `string` | Origen. |
| `variety` | `string` | Variedad. |
| `processing` | `string` | Procesamiento. |
| `sessionDate` | `string` | Fecha de la sesion. |
| `favorite` | `boolean` | Marcada como favorita. |
| `resultsJson` | `string` | Resultados serializados. |
| `roastStyleNotes` | `string` | Notas adicionales. |

### Extra Response

No aplica.

### Error 4xx

Error 400:

```json
{
  "message": "name is required"
}
```

```json
{
  "message": "No se pudo crear la sesión"
}
```

---

<h2 style="color:#0A58CA;">Cupping Sessions - Listar sesiones por usuario</h2>

<p>
<span style="background:#0A58CA;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">GET</span>
<code>/api/v1/cupping-sessions/{userId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. Usuario cuyas sesiones se listan (mas recientes primero). |

### Request Example

```http
GET /api/v1/cupping-sessions/9301
Accept: application/json
```

### Success Response

**HTTP 200**

Arreglo de `CuppingSessionResource`.

### Default Response

Igual que **Cupping Sessions - Crear sesion de cata**.

### Extra Response

No aplica.

### Error 4xx

Listado vacio devuelve `[]`.

---

<h2 style="color:#0A58CA;">Cupping Sessions - Obtener sesion por id</h2>

<p>
<span style="background:#0A58CA;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">GET</span>
<code>/api/v1/cupping-sessions/user/{userId}/{sessionId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. |
| `sessionId` | `number` | Requerido. |

### Request Example

```http
GET /api/v1/cupping-sessions/user/9301/1
Accept: application/json
```

### Success Response

**HTTP 200**

Objeto `CuppingSessionResource`.

### Default Response

Igual que **Cupping Sessions - Crear sesion de cata**.

### Extra Response

No aplica.

### Error 4xx

Error 404:

```json
{
  "message": "Sesión de cata no encontrada"
}
```

---

<h2 style="color:#0A58CA;">Cupping Sessions - Actualizar sesion</h2>

<p>
<span style="background:#6F42C1;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">PUT</span>
<code>/api/v1/cupping-sessions/user/{userId}/{sessionId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Content-Type` | Requerido. Usar `application/json`. |
| `Accept` | Opcional. Recomendado `application/json`. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. |
| `sessionId` | `number` | Requerido. |

### Parametro (body)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `name` | `string` | Requerido. |
| `origin` | `string` | Requerido. |
| `variety` | `string` | Requerido. |
| `processing` | `string` | Requerido. |
| `sessionDate` | `string` | Requerido. `YYYY-MM-DD`. |
| `favorite` | `boolean` | Requerido en el body (tipo primitivo). |
| `resultsJson` | `string` | `Opcional`. |
| `roastStyleNotes` | `string` | `Opcional`. |

### Request Example

```json
{
  "name": "Sesion del user 9302",
  "origin": "Chile",
  "variety": "Bourbon",
  "processing": "washed",
  "sessionDate": "2026-05-29",
  "favorite": false,
  "resultsJson": "{\"aroma\":5,\"cuerpo\":5}"
}
```

### Success Response

**HTTP 200**

Objeto `CuppingSessionResource` actualizado.

### Default Response

Igual que **Cupping Sessions - Crear sesion de cata**.

### Extra Response

No aplica.

### Error 4xx

Error 400:

```json
{
  "message": "sessionDate is required"
}
```

Error 404:

```json
{
  "message": "Sesión de cata no encontrada"
}
```

---

<h2 style="color:#0A58CA;">Cupping Sessions - Eliminar sesion</h2>

<p>
<span style="background:#DC3545;color:#ffffff;padding:4px 10px;border-radius:999px;font-weight:700;">DELETE</span>
<code>/api/v1/cupping-sessions/user/{userId}/{sessionId}</code>
</p>

### Header

| Campo | Descripcion |
| --- | --- |
| `Accept` | Opcional. |

### Parametro (path)

| Campo | Tipo | Descripcion |
| --- | --- | --- |
| `userId` | `number` | Requerido. |
| `sessionId` | `number` | Requerido. |

### Request Example

```http
DELETE /api/v1/cupping-sessions/user/9301/1
```

### Success Response

**HTTP 204**

Sin cuerpo.

### Default Response

No aplica.

### Extra Response

No aplica.

### Error 4xx

Error 404:

```json
{
  "message": "Sesión de cata no encontrada"
}
```

---

## Notas Finales de Implementacion

- Los controllers de este servicio son RecipesController, PortfoliosController, IngredientsController y CuppingSessionsController.
- La base de datos de Procedure contiene cupping_sessions, recipes, portfolios e ingredients.
- El servicio conserva las rutas, validaciones y respuestas de los modulos originales.
- RecipesController declara unauthorized() pero no lo invoca; la seguridad local no filtra por JWT.
- Rutas de cupping mezclan /{userId} para listar y user/{userId}/{sessionId} para detalle, actualizar y eliminar.
- Al crear receta, la respuesta incluye ingredients cargados desde persistencia.
- UpdateIngredientResource exige amount > 0, pero CreateIngredientResource solo exige que amount no sea null.
- Pruebas funcionales: PreparationFunctionalTest y CuppingSessionsFunctionalTest.

## Separacion de microservicios

Segun la seccion 5.4 del informe, este repositorio conserva unicamente cuppingSession, preparation y las utilidades shared.
Calibraciones se ejecuta en [calibrations-cafelab](../calibrations-cafelab/README.md) y la biblioteca de defectos en [defects-cafelab](../defects-cafelab/README.md).
Los tres servicios se registran por separado en Eureka y el gateway conserva las URLs publicas originales.

DB_URL permite configurar una base PostgreSQL exclusiva de Procedure; si no se define, se conserva la URL Supabase anterior.
DB_USERNAME, DB_PASSWORD y SUPABASE_PROJECT_REF mantienen su comportamiento anterior.
La extraccion de codigo no modifica datos ni elimina tablas de una base existente. Antes de cambiar DB_URL, seguir [la guia de migracion](../calibrations-cafelab/MIGRATION.md).

