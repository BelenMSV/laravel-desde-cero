# Tema 3: El Objeto Request (Capturando Datos)

Hasta ahora, nuestra aplicación solo ha enviado información al usuario (mostrar vistas con variables estáticas). Pero en la vida real, la comunicación es de doble vía. Los usuarios nos envían información constantemente: al llenar un formulario de registro, al buscar un producto o al modificar su perfil.

Para capturar, leer y procesar toda esa información que el navegador del usuario nos envía, Laravel nos proporciona una herramienta indispensable: **el objeto `Request`**.

## 📥 ¿Qué es el Request?

En el desarrollo web, cada vez que un usuario hace clic en un enlace, escribe algo en la URL o envía un formulario, está haciendo una **petición HTTP** (*HTTP Request*). 

Laravel toma toda esa petición (los datos del formulario, los parámetros de la URL, la dirección IP del usuario, los archivos adjuntos) y la empaqueta ordenadamente dentro de una clase de PHP llamada `Illuminate\Http\Request`.

## 💉 Inyectando el Request en el Controlador

Para poder usar este objeto mágico, necesitamos pedirle a Laravel que nos lo entregue dentro de nuestro método. A esto se le llama **Inyección de Dependencias**.

Abre tu `UserController.php` y vamos a crear un nuevo método que simule la búsqueda de un usuario:

```php
<?php

namespace App\Http\Controllers;

// 1. Asegúrate de que esta línea exista en la parte superior (Artisan la pone por defecto)
use Illuminate\Http\Request; 

class UserController extends Controller
{
    // ... método index() que creamos en el tema anterior ...

    // 2. Inyectamos la clase Request como parámetro del método
    public function buscar(Request $request)
    {
        // Ahora tenemos acceso a todo lo que envió el usuario dentro de la variable $request
        
        // Obtenemos el valor del campo 'termino' que viene en la URL
        $busqueda = $request->input('termino');

        return "Estás buscando al usuario: " . $busqueda;
    }
}
```

## 🎣 Métodos más útiles del Request

El objeto `$request` tiene muchísimos métodos útiles para extraer información. Aquí tienes los que usarás en tu día a día:

### 1. Obtener un dato específico
Si tienes un formulario con un campo `<input name="email">`, lo capturas así en tu controlador:
```php
$email = $request->input('email');

// Sintaxis dinámica (más corta y muy utilizada por la comunidad):
$email = $request->email;
```

### 2. Obtener un dato con valor por defecto
Si el usuario no envió ese dato específico, puedes definir un "plan B" para evitar errores:
```php
$rol = $request->input('rol', 'invitado'); // Si no hay rol en la petición, será 'invitado'
```

### 3. Obtener TODOS los datos
Muy útil cuando quieres ver exactamente qué está llegando o cuando guardaremos todo el formulario de golpe en la base de datos (lo veremos en próximos módulos):
```php
$todosLosDatos = $request->all(); // Devuelve un arreglo (array) con toda la información
```

### 4. Preguntar si un dato existe
```php
if ($request->has('telefono')) {
    // Ejecutar lógica extra solo si el usuario decidió enviar su teléfono
}
```

## 🔗 Probando nuestro método

Para que nuestro nuevo método `buscar` funcione, necesitamos agregar su ruta correspondiente en el archivo `routes/web.php`:

```php
// Añadimos esta ruta debajo de las que ya teníamos
Route::get('/buscar-usuario', [UserController::class, 'buscar'])->name('usuario.buscar');
```

Si ahora vas a tu navegador y escribes los parámetros directamente en la URL así:
`http://localhost:8000/buscar-usuario?termino=Maria`

Verás en pantalla el texto: *"Estás buscando al usuario: Maria"*. ¡Felicidades! Has capturado tu primer dato proveniente del usuario utilizando el poderoso objeto Request.

---

### 🚀 Siguiente paso
Ahora sabes cómo crear métodos manuales en un controlador y cómo recibir datos. Pero si quisieras crear un sistema completo para gestionar artículos de un blog (verlos, crearlos, guardarlos, editarlos, actualizarlos y borrarlos), tendrías que escribir 7 métodos distintos y 7 rutas manuales. ¿No hay una forma más rápida y estandarizada? ¡Sí la hay! En el siguiente tema descubriremos la magia de los **Controladores de Recursos**.