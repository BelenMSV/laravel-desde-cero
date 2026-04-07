# Tema 1: Formularios y Seguridad CSRF

Hasta ahora, la única forma que teníamos de interactuar con nuestra aplicación era escribiendo la URL en el navegador (peticiones GET). Pero para enviar información (como registrar un usuario o crear un Post), necesitamos usar formularios y peticiones POST.

## 📝 1. Creando las Rutas

Para manejar un formulario, siempre necesitamos **dos rutas**:
1. Una ruta `GET` para **mostrar** la página con el formulario HTML.
2. Una ruta `POST` para **recibir y procesar** los datos cuando el usuario haga clic en "Enviar".

Abre tu archivo `routes/web.php` y añade esto:

```php
use Illuminate\Http\Request;

// 1. Ruta para mostrar el formulario
Route::get('/contacto', function () {
    return view('contacto');
})->name('contacto.mostrar');

// 2. Ruta para procesar los datos
Route::post('/contacto', function (Request $request) {
    // Aquí procesaremos los datos más adelante
    return "Has enviado el mensaje: " . $request->input('mensaje');
})->name('contacto.enviar');
```
*Nota: Fíjate que ambas rutas pueden compartir la misma URL (`/contacto`) porque usan métodos HTTP distintos (`get` y `post`).*

## 🛡️ 2. El Formulario y el problema de Seguridad

Vamos a crear nuestra vista. Crea un archivo `resources/views/contacto.blade.php` e introduce este formulario básico:

```html
<form action="{{ route('contacto.enviar') }}" method="POST">
    
    <label>Escribe tu mensaje:</label>
    <input type="text" name="mensaje">
    
    <button type="submit">Enviar</button>

</form>
```

Si pruebas este código en tu navegador y le das a enviar, **Laravel te mostrará una pantalla de error diciendo "419 | PAGE EXPIRED"**. 

¿Por qué? Porque Laravel acaba de protegerte de un ataque hacker.



## 🕵️‍♂️ 3. ¿Qué es el ataque CSRF?

**CSRF** (*Cross-Site Request Forgery* o Falsificación de Petición a través de Sitios) es un ataque donde una web maliciosa engaña a tu navegador para que envíe un formulario a tu aplicación de Laravel sin tu permiso (por ejemplo, para borrar tu cuenta o hacer una transferencia bancaria).

Para evitarlo, Laravel exige que **todo formulario POST, PUT, PATCH o DELETE** incluya un "token secreto". Si el formulario no tiene el token, Laravel asume que es un ataque y devuelve el error 419.

## 🔑 4. La solución: La directiva `@csrf`

Añadir este token secreto es absurdamente fácil en Laravel. Solo tienes que escribir la directiva `@csrf` justo debajo de la etiqueta `<form>`:

```html
<form action="{{ route('contacto.enviar') }}" method="POST">
    @csrf <label>Escribe tu mensaje:</label>
    <input type="text" name="mensaje">
    
    <button type="submit">Enviar</button>

</form>
```

Si inspeccionas el código fuente en tu navegador, verás que `@csrf` se transforma automáticamente en un campo oculto con una contraseña larguísima generada para ese usuario específico:
`<input type="hidden" name="_token" value="abc123xyz...">`

¡Vuelve a probar tu formulario y verás que ahora sí funciona perfectamente!

---

### 🚀 Siguiente paso
Sabemos cómo enviar información usando el método POST. Pero según el estándar web, si queremos "Actualizar" un dato debemos usar `PUT`, y si queremos "Borrarlo" debemos usar `DELETE`. El problema es que los navegadores HTML no entienden estos métodos. En el siguiente tema, aprenderemos cómo solucionar esto usando el **Spoofing de Métodos**.