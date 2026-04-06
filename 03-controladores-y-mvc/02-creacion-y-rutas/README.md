# Tema 2: Creación y Rutas de Controladores

En el tema anterior aprendimos la teoría del patrón MVC. Ahora vamos a aplicarla. El objetivo de este tema es sacar la lógica (las funciones anónimas) de nuestro archivo `web.php` y moverla a un **Controlador**.

## 🛠️ 1. Creando el Controlador con Artisan

Por regla general, **nunca debes crear el archivo de un controlador manualmente**. Para eso tenemos a nuestro asistente de terminal, **Artisan**, que nos creará la clase con la estructura correcta.

Abre tu terminal (asegúrate de estar en la raíz de tu proyecto) y ejecuta:

```bash
php artisan make:controller UserController
```

**Convenciones importantes de Laravel:**
* Los nombres de los controladores siempre deben ir en **PascalCase** (la primera letra de cada palabra en mayúscula, sin espacios).
* Siempre deben terminar con la palabra `Controller` (por ejemplo: `ProductController`, `BillingController`, `UserController`).

## ✍️ 2. Escribiendo la lógica en el Controlador

Artisan acaba de crear un archivo nuevo para ti. Ve a la carpeta `app/Http/Controllers/` y abre tu nuevo archivo `UserController.php`. 

Verás una clase vacía. Vamos a crear un método (función) público llamado `index` (es el nombre estándar para mostrar la página principal de un recurso) y vamos a devolver la vista que habíamos creado en el módulo anterior:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    // Creamos nuestro método
    public function index()
    {
        $nombreUsuario = 'Carlos';
        
        // Retornamos la vista exactamente como lo hacíamos en web.php
        return view('perfil', [
            'nombre' => $nombreUsuario
        ]);
    }
}
```

## 🔗 3. Conectando la Ruta con el Controlador

Ahora tenemos que decirle a nuestro archivo de rutas que deje de usar funciones anónimas y empiece a usar nuestro nuevo `UserController`.

Abre `routes/web.php` y realiza estos dos cambios vitales:

### Paso A: Importar el Controlador
En la parte superior del archivo, debes decirle a PHP dónde vive tu controlador usando la palabra `use`. Si no haces esto, Laravel te lanzará un error diciendo que la clase no existe.

```php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController; // <-- Añade esta línea
```

### Paso B: Actualizar la Ruta
Ahora cambiaremos la definición de la ruta. Usaremos la sintaxis de "arreglo" `[NombreClase::class, 'nombreDelMetodo']`:

```php
// ANTES (Mala práctica en proyectos grandes):
// Route::get('/mi-perfil', function () {
//     return view('perfil');
// });

// AHORA (Buena práctica y código limpio):
Route::get('/mi-perfil', [UserController::class, 'index'])->name('perfil.usuario');
```

**¿Qué está pasando aquí?**
Le estamos diciendo a Laravel: *"Cuando alguien visite `/mi-perfil` a través del método GET, busca la clase `UserController` y ejecuta su método `index`"*.

¡Si vas a tu navegador y visitas tu ruta, tu página web debería funcionar exactamente igual que antes, pero ahora tienes una arquitectura profesional, escalable y muy limpia en tu archivo `web.php`!

---

### 🚀 Siguiente paso
Nuestro controlador ya puede devolver vistas y pasar variables predefinidas, pero... ¿cómo hace el controlador para recibir información que el usuario le envía? Por ejemplo, cuando alguien rellena un formulario o envía datos por la URL. En el siguiente tema, descubriremos el poderoso objeto **Request**.