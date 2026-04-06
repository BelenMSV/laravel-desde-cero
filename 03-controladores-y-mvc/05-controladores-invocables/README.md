# Tema 5: Controladores Invocables (Single Action Controllers)

En el tema anterior vimos cómo los Controladores de Recursos son perfectos para gestionar todo el ciclo de vida de un elemento (como los Posts o los Usuarios). Sin embargo, a veces nos encontramos con acciones muy específicas que no encajan en el modelo tradicional de un CRUD.

Por ejemplo, imagina que necesitas una ruta exclusiva para procesar el pago de un carrito de compras, o para generar y descargar un reporte en PDF. 

Para estos casos de **"una sola acción"**, Laravel nos ofrece los **Controladores Invocables**.

## 🎯 ¿Qué es un Controlador Invocable?

Es un controlador que tiene **un único propósito y un único método**. Sigue a rajatabla el "Principio de Responsabilidad Única" (Single Responsibility Principle) de la programación limpia.

Al tener una sola tarea, tu código se vuelve extremadamente fácil de leer, testear y mantener.

## 🛠️ 1. Creando un Controlador Invocable

Para generar este tipo de controlador, usamos Artisan con la bandera `--invokable` (o `-i`):

```bash
php artisan make:controller ProcesarPagoController --invokable
```

## 🪄 2. El Método Mágico `__invoke`

Si abres el archivo que se acaba de crear en `app/Http/Controllers/ProcesarPagoController.php`, notarás algo diferente. En lugar de un método `index` o `store`, Artisan generó automáticamente un método llamado `__invoke`:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ProcesarPagoController extends Controller
{
    /**
     * Handle the incoming request.
     */
    public function __invoke(Request $request)
    {
        // Aquí va toda la lógica exclusiva para procesar el pago.
        // Como es la única tarea de este archivo, no se mezclará
        // con la lógica de ver productos o editar perfiles.
        
        return "El pago se ha procesado con éxito.";
    }
}
```

`__invoke` es un "método mágico" nativo de PHP. Le dice al lenguaje que, si alguien intenta usar la clase como si fuera una función normal, debe ejecutar este bloque de código.

## 🔗 3. Una Ruta más limpia

La mayor ventaja de los Controladores Invocables se ve en tu archivo de rutas (`routes/web.php`). 

Como este controlador solo tiene un método, **ya no necesitas usar la sintaxis de arreglo** (`[Controlador::class, 'metodo']`). Simplemente pasas el nombre de la clase, y Laravel sabrá automáticamente que debe ejecutar el método `__invoke`.

```php
use App\Http\Controllers\ProcesarPagoController;

// Fíjate en lo limpia que queda la ruta:
Route::post('/pagar', ProcesarPagoController::class)->name('pago.procesar');
```

## 💡 ¿Cuándo usar cada tipo de Controlador?

* **Controlador Normal:** Para agrupar unas pocas funciones relacionadas (ej. `ContactoController` con `mostrarFormulario` y `enviarCorreo`).
* **Controlador de Recursos:** Cuando necesitas un CRUD completo (7 métodos).
* **Controlador Invocable:** Cuando la acción es única, compleja o muy específica (ej. `GenerarFacturaPdfController`, `CambiarIdiomaController`).

---

### 🎉 ¡Módulo 3 Completado!

¡Enhorabuena! Has dominado el patrón MVC y la arquitectura fundamental de Laravel. Ahora sabes cómo interceptar las peticiones del usuario con el objeto `Request`, organizar tu lógica en Controladores y estructurar tus rutas de forma profesional.

Pero hasta ahora, todos nuestros datos han sido estáticos (textos escritos en el código). En el **Módulo 4: Base de Datos y Eloquent**, daremos el paso definitivo: conectaremos nuestra aplicación a una base de datos real para guardar, leer y modificar información de forma permanente.