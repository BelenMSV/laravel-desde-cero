# Tema 3: Rutas y el Controlador de Recursos

Tenemos la base de datos lista y llena de productos de prueba. El siguiente paso natural es crear el Controlador que se encargará de gestionar toda la lógica (mostrar productos, guardarlos, borrarlos) y las Rutas para acceder a él.

## 🧠 1. Creando el Controlador de Recursos

Como estamos construyendo un CRUD estándar (Create, Read, Update, Delete) para nuestros productos, no vamos a crear un controlador en blanco. Vamos a pedirle a Laravel que nos genere un **Controlador de Recursos**.

Abre tu terminal y ejecuta:

```bash
php artisan make:controller ProductoController -r
```

La bandera `-r` (o `--resource`) es mágica. Si abres el nuevo archivo `app/Http/Controllers/ProductoController.php`, verás que Laravel ya ha escrito por ti la estructura de los 7 métodos estándar que toda aplicación profesional necesita:
* `index()`: Para listar todos los productos.
* `create()`: Para mostrar el formulario de creación.
* `store()`: Para guardar el producto en la base de datos.
* `show()`: Para mostrar un solo producto al detalle.
* `edit()`: Para mostrar el formulario de edición.
* `update()`: Para guardar los cambios editados.
* `destroy()`: Para borrar el producto.

## 🛣️ 2. Configurando la Ruta

Ahora necesitamos conectar las URLs del navegador con este nuevo controlador. Si lo hiciéramos manualmente, tendríamos que escribir 7 líneas de código en nuestro archivo de rutas (una para cada método). 

Afortunadamente, Laravel tiene un atajo brillante para esto. Abre tu archivo `routes/web.php` y añade lo siguiente:

```php
<?php

use Illuminate\Support\Facades\Route;
// 1. IMPORTANTE: No olvides importar tu controlador arriba
use App\Http\Controllers\ProductoController; 

Route::get('/', function () {
    return view('welcome');
});

// 2. Ruta de Recursos para los productos
Route::resource('productos', ProductoController::class);
```

¡Eso es todo! Con esa única línea `Route::resource`, Laravel acaba de crear 7 rutas diferentes por detrás.

## 🗺️ 3. Comprobando el Mapa de Rutas

Para asegurarnos de que todo ha funcionado y entender qué URLs acaba de crear Laravel para nosotros, vamos a usar un comando súper útil de Artisan.

En tu terminal ejecuta:
```bash
php artisan route:list --path=productos
```

Deberías ver una tabla similar a esta (esto es tu mapa de carreteras):

| Method | URI | Name | Action |
| :--- | :--- | :--- | :--- |
| GET | `productos` | **productos.index** | ProductoController@index |
| POST | `productos` | **productos.store** | ProductoController@store |
| GET | `productos/create` | **productos.create** | ProductoController@create |
| GET | `productos/{producto}` | **productos.show** | ProductoController@show |
| PUT/PATCH | `productos/{producto}` | **productos.update** | ProductoController@update |
| DELETE | `productos/{producto}` | **productos.destroy** | ProductoController@destroy |
| GET | `productos/{producto}/edit` | **productos.edit** | ProductoController@edit |

Fíjate en la columna **"Name"** (`productos.index`, `productos.create`, etc.). Estos son los nombres que usaremos en nuestras vistas de Blade con la función `route()` para crear enlaces y formularios sin preocuparnos por si la URL cambia en el futuro.

## 🧪 4. Una pequeña prueba

Vamos a comprobar que nuestro controlador ya está hablando con la base de datos. Abre `ProductoController.php` y modifica el método `index` para devolver todos los productos:

```php
// No olvides importar el modelo arriba
use App\Models\Producto;

public function index()
{
    // Traemos todos los productos de la base de datos
    $productos = Producto::all();
    
    // Por ahora, solo los imprimimos en pantalla para probar
    return $productos;
}
```

Si enciendes tu servidor (`php artisan serve`) y vas a `http://localhost:8000/productos` en tu navegador, deberías ver todo un bloque de texto (JSON) con los 50 productos que creamos en el tema anterior con los Seeders.

¡La conexión es un éxito!

---

### 🚀 Siguiente paso
Devolver texto crudo está bien para probar, pero nuestros usuarios necesitan una interfaz bonita. En el siguiente tema, diseñaremos nuestra **Plantilla Maestra (Layout)** y pasaremos esos datos a una vista Blade para mostrar nuestro catálogo en una cuadrícula profesional, implementando además la **Paginación automática**.