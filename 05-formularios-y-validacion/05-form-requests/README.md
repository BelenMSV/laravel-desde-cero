# Tema 5: Form Requests (El Estándar Profesional)

En el Tema 3 vimos cómo validar datos usando `$request->validate()` directamente dentro de nuestro Controlador. 

Ese método funciona perfectamente, pero tiene un problema a largo plazo: **viola el Principio de Responsabilidad Única**. El trabajo del Controlador debería ser únicamente coordinar la petición y devolver una respuesta, no encargarse de comprobar si una contraseña tiene 8 caracteres o si un email es válido.

Si tienes un formulario de registro con 20 campos, tu Controlador se convertirá en un archivo gigante e ilegible. Para solucionar esto, Laravel inventó los **Form Requests**.



## 🏗️ 1. ¿Qué es un Form Request?

Un Form Request es una clase especial (un archivo independiente) cuya única misión en la vida es verificar que los datos enviados por el usuario sean correctos **antes** de que siquiera lleguen a tu Controlador.

Si la validación falla, el Form Request rebota al usuario de vuelta al formulario automáticamente, y tu Controlador ni siquiera se entera de que hubo una petición fallida. ¡Esto mantiene tu código increíblemente limpio!

## 🛠️ 2. Creando nuestro primer Form Request

Vamos a crear un validador específico para nuestro formulario de creación de Posts. En tu terminal ejecuta:

```bash
php artisan make:request StorePostRequest
```

Esto creará una nueva carpeta y un archivo en `app/Http/Requests/StorePostRequest.php`.

## ✍️ 3. Configurando las Reglas

Si abres tu nuevo archivo, verás que tiene dos métodos principales: `authorize()` y `rules()`.

1. **`authorize()`**: Sirve para la seguridad. Aquí decides si el usuario actual tiene permiso para hacer esta acción (por ejemplo, comprobar si es administrador). Por ahora, como es un formulario público, cambiaremos el `return false` por `return true`.
2. **`rules()`**: Aquí es donde pegaremos las reglas de validación que antes teníamos en el Controlador.

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        // 1. Cambiamos esto a TRUE para permitir que se ejecute la validación
        return true; 
    }

    public function rules(): array
    {
        // 2. Definimos nuestras reglas
        return [
            'titulo' => 'required|min:5|max:255',
            'contenido' => 'required',
            'email_autor' => 'required|email|unique:users,email'
        ];
    }
}
```

## 🧹 4. Limpiando el Controlador

Ahora viene la magia. Vamos a nuestro Controlador (`PostController.php`) y vamos a borrar todo ese bloque enorme de validación.

Lo único que tenemos que hacer es cambiar el tipo de dato que recibe el método `store`. En lugar de recibir el genérico `Request`, le diremos que reciba nuestro nuevo `StorePostRequest`.

```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
// 1. Importamos nuestra nueva clase en la parte superior
use App\Http\Requests\StorePostRequest; 

class PostController extends Controller
{
    // 2. Cambiamos "Request" por "StorePostRequest"
    public function store(StorePostRequest $request) 
    {
        // Si el código llega a esta línea, significa que los datos
        // YA ESTÁN VALIDADOS y son 100% seguros. ¡Magia!

        // Podemos usar $request->validated() para obtener solo los campos seguros
        Post::create($request->validated());

        return "Post creado con éxito";
    }
}
```

¿Ves lo limpio y profesional que queda el Controlador ahora? Solo tiene dos líneas de código. Toda la "basura" de la validación está escondida y organizada en su propio archivo.

## 🗣️ Bonus: Personalizando los Mensajes

Una de las grandes ventajas de los Form Requests es que puedes añadir un tercer método llamado `messages()` para traducir o personalizar los mensajes de error de forma muy sencilla:

```php
// Dentro de StorePostRequest.php
public function messages(): array
{
    return [
        'titulo.required' => 'Oye, ¡no puedes publicar un artículo sin título!',
        'titulo.min' => 'El título es demasiado corto, esfuérzate un poco más.',
        'email_autor.unique' => 'Ese correo ya está registrado en nuestro sistema.'
    ];
}
```

---

### 🚀 Siguiente paso
Con esto, tu aplicación ya es capaz de recibir textos y validarlos como todo un profesional. Pero, ¿qué pasa cuando queremos que el usuario nos envíe un archivo físico, como una foto de perfil o un PDF? En el último tema de este módulo, desmitificaremos la **Subida de Archivos e Imágenes** en Laravel.