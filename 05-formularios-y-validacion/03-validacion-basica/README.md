# Tema 3: Validación Básica

Imagina que tienes un formulario para crear un "Post" con un `titulo` y un `contenido`. ¿Qué pasa si el usuario le da al botón de enviar sin haber escrito nada? Si intentas guardar eso en la base de datos, lo más probable es que tu aplicación explote y muestre un error fatal en la pantalla.

Para evitar esto, Laravel tiene un sistema de **Validación** increíblemente fácil de usar.



## 🛡️ 1. El método `$request->validate()`

La validación se realiza en el **Controlador**, justo en el método que recibe los datos del formulario (generalmente el método `store` o `update`).

Para validar, usamos el método `validate()` que viene incluido en el objeto `$request`. Le pasamos un arreglo donde las **llaves** son los nombres de los campos de tu formulario (`name="titulo"`), y los **valores** son las reglas que deben cumplir.

Abre tu Controlador y fíjate en esto:

```php
use Illuminate\Http\Request;
use App\Models\Post;

public function store(Request $request)
{
    // 1. VALIDACIÓN
    // Separamos múltiples reglas usando el símbolo "pipe" (|)
    $datosValidados = $request->validate([
        'titulo' => 'required|min:5|max:255',
        'contenido' => 'required',
        'email_autor' => 'required|email|unique:users,email'
    ]);

    // 2. GUARDAR EN BASE DE DATOS
    // ¡Ojo! El código de abajo NUNCA se ejecutará si la validación falla.
    Post::create($datosValidados);

    return "Post creado con éxito";
}
```

## 📏 2. Las Reglas de Validación más comunes

Laravel trae decenas de reglas de validación listas para usar. Aquí tienes las que usarás todos los días:

| Regla | ¿Qué hace? |
| :--- | :--- |
| `required` | El campo no puede estar vacío. |
| `email` | El campo debe tener un formato de correo válido (usuario@dominio.com). |
| `min:X` | Debe tener al menos X caracteres (o si es un número, ser mayor que X). |
| `max:X` | No puede tener más de X caracteres. |
| `numeric` | El campo debe ser un número. |
| `string` | El campo debe ser texto. |
| `confirmed` | Se usa para contraseñas. Exige que haya otro campo llamado `password_confirmation` y que ambos coincidan. |
| `unique:tabla,columna` | ¡Magia pura! Revisa en la base de datos que ese valor no exista ya (ideal para evitar emails duplicados). |

*Si quieres ver la lista completa, puedes buscar "Available Validation Rules" en la documentación oficial de Laravel.*

## 🪄 3. ¿Qué pasa cuando la validación falla?

Aquí es donde Laravel brilla. Si escribieras PHP puro, tendrías que crear una redirección manual, almacenar los mensajes de error en la sesión y devolver al usuario a la página anterior.

**En Laravel, todo esto es automático.** Si alguna de las reglas que pusiste en `$request->validate()` no se cumple, Laravel detiene inmediatamente la ejecución del controlador, **redirige al usuario automáticamente de vuelta al formulario** y guarda los mensajes de error en la sesión de forma invisible.

---

### 🚀 Siguiente paso
Sabemos que Laravel redirige al usuario de vuelta si se equivoca, ¡pero el usuario no lo sabe! Si el formulario simplemente se recarga vacío, el usuario pensará que la página está rota. En el siguiente tema, aprenderemos cómo atrapar esos errores automáticos de Laravel y mostrarlos en pantalla, además de usar la fantástica función **`old()`** para que el usuario no pierda lo que ya había escrito.