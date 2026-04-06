# Tema 4: Controladores de Recursos (Resource Controllers)

Imagina que quieres gestionar los "Post" de un blog. Necesitarías:
1. Una página para ver todos los posts.
2. Una página para crear uno nuevo.
3. Un proceso para guardar ese post.
4. Una página para ver un post específico... y así hasta 7 acciones.

Si lo haces manualmente, tendrías que escribir 7 rutas en `web.php` y 7 métodos en tu controlador. **Los Controladores de Recursos automatizan todo este proceso por ti.**

## 🛠️ 1. Creando un Controlador de Recurso

Para crear un controlador que ya venga con los 7 métodos estándar preparados, usamos la bandera `-r` o `--resource` en Artisan:

```bash
php artisan make:controller PostController -r
```

Si abres el archivo `app/Http/Controllers/PostController.php`, verás que Laravel ya escribió por ti las funciones: `index`, `create`, `store`, `show`, `edit`, `update` y `destroy`.

## 📋 2. Los 7 Métodos Mágicos

Aquí tienes el "mapa" de lo que hace cada método. Es el estándar que siguen todos los desarrolladores de Laravel en el mundo:

| Verbo HTTP | URL | Método | Propósito |
| :--- | :--- | :--- | :--- |
| **GET** | `/posts` | `index` | Listar todos los posts. |
| **GET** | `/posts/create` | `create` | Mostrar el formulario para crear un post. |
| **POST** | `/posts` | `store` | Guardar el nuevo post en la base de datos. |
| **GET** | `/posts/{id}` | `show` | Mostrar un post específico. |
| **GET** | `/posts/{id}/edit` | `edit` | Mostrar el formulario para editar un post. |
| **PUT/PATCH** | `/posts/{id}` | `update` | Actualizar los cambios del post. |
| **DELETE** | `/posts/{id}` | `destroy` | Eliminar el post por completo. |

## 🔗 3. La Ruta Maestra: `Route::resource`

Lo más increíble es que no tienes que registrar las 7 rutas una por una en tu archivo `web.php`. Laravel tiene una función especial que las crea todas de golpe.

Abre `routes/web.php` y añade:

```php
use App\Http\Controllers\PostController;

// Esta única línea registra las 7 rutas de la tabla anterior
Route::resource('posts', PostController::class);
```

### 💡 Ventajas de usar Recursos:
1. **Código Limpio:** Tu archivo de rutas se mantiene pequeño aunque tu aplicación tenga cientos de secciones.
2. **Nombres Automáticos:** Laravel ya les pone nombre a las rutas por ti. Por ejemplo, la ruta para ir al listado siempre será `route('posts.index')` y la de crear será `route('posts.create')`.
3. **Previsibilidad:** Cualquier programador que entre a tu proyecto sabrá exactamente dónde encontrar la lógica de "editar" o "borrar".

## 🔍 Verificando tus rutas

Si alguna vez tienes dudas de qué rutas creó Laravel para tu recurso, puedes ejecutar este comando en la terminal:

```bash
php artisan route:list
```
Verás una lista con todas las URLs, nombres y controladores de tu aplicación.

---

### 🚀 Siguiente paso
Los controladores de recursos son ideales cuando tienes muchas acciones (CRUD). Pero, ¿qué pasa cuando un controlador solo tiene que hacer **una sola cosa**? Por ejemplo, procesar un pago o enviar un reporte. En el último tema de este módulo, aprenderemos a crear **Controladores Invocables**.# Tema 4: Controladores de Recursos (Resource Controllers)

Imagina que quieres gestionar los "Post" de un blog. Necesitarías:
1. Una página para ver todos los posts.
2. Una página para crear uno nuevo.
3. Un proceso para guardar ese post.
4. Una página para ver un post específico... y así hasta 7 acciones.

Si lo haces manualmente, tendrías que escribir 7 rutas en `web.php` y 7 métodos en tu controlador. **Los Controladores de Recursos automatizan todo este proceso por ti.**

## 🛠️ 1. Creando un Controlador de Recurso

Para crear un controlador que ya venga con los 7 métodos estándar preparados, usamos la bandera `-r` o `--resource` en Artisan:

```bash
php artisan make:controller PostController -r
```

Si abres el archivo `app/Http/Controllers/PostController.php`, verás que Laravel ya escribió por ti las funciones: `index`, `create`, `store`, `show`, `edit`, `update` y `destroy`.

## 📋 2. Los 7 Métodos Mágicos

Aquí tienes el "mapa" de lo que hace cada método. Es el estándar que siguen todos los desarrolladores de Laravel en el mundo:

| Verbo HTTP | URL | Método | Propósito |
| :--- | :--- | :--- | :--- |
| **GET** | `/posts` | `index` | Listar todos los posts. |
| **GET** | `/posts/create` | `create` | Mostrar el formulario para crear un post. |
| **POST** | `/posts` | `store` | Guardar el nuevo post en la base de datos. |
| **GET** | `/posts/{id}` | `show` | Mostrar un post específico. |
| **GET** | `/posts/{id}/edit` | `edit` | Mostrar el formulario para editar un post. |
| **PUT/PATCH** | `/posts/{id}` | `update` | Actualizar los cambios del post. |
| **DELETE** | `/posts/{id}` | `destroy` | Eliminar el post por completo. |

## 🔗 3. La Ruta Maestra: `Route::resource`

Lo más increíble es que no tienes que registrar las 7 rutas una por una en tu archivo `web.php`. Laravel tiene una función especial que las crea todas de golpe.

Abre `routes/web.php` y añade:

```php
use App\Http\Controllers\PostController;

// Esta única línea registra las 7 rutas de la tabla anterior
Route::resource('posts', PostController::class);
```

### 💡 Ventajas de usar Recursos:
1. **Código Limpio:** Tu archivo de rutas se mantiene pequeño aunque tu aplicación tenga cientos de secciones.
2. **Nombres Automáticos:** Laravel ya les pone nombre a las rutas por ti. Por ejemplo, la ruta para ir al listado siempre será `route('posts.index')` y la de crear será `route('posts.create')`.
3. **Previsibilidad:** Cualquier programador que entre a tu proyecto sabrá exactamente dónde encontrar la lógica de "editar" o "borrar".

## 🔍 Verificando tus rutas

Si alguna vez tienes dudas de qué rutas creó Laravel para tu recurso, puedes ejecutar este comando en la terminal:

```bash
php artisan route:list
```
Verás una lista con todas las URLs, nombres y controladores de tu aplicación.

---

### 🚀 Siguiente paso
Los controladores de recursos son ideales cuando tienes muchas acciones (CRUD). Pero, ¿qué pasa cuando un controlador solo tiene que hacer **una sola cosa**? Por ejemplo, procesar un pago o enviar un reporte. En el último tema de este módulo, aprenderemos a crear **Controladores Invocables**.