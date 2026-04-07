# Tema 3: Modelos y Eloquent ORM

Si la base de datos es la "memoria" de nuestra aplicación, los **Modelos** son los mensajeros. Son las clases de PHP encargadas de hablar directamente con las tablas de tu base de datos.

En Laravel, este sistema de comunicación se llama **Eloquent**. Es un ORM (*Object-Relational Mapper*), lo que significa que transforma las filas de tus tablas SQL en objetos de PHP con los que es un placer trabajar.



## 🧠 El concepto de Eloquent

En lugar de escribir esto (SQL puro):
```sql
SELECT * FROM posts WHERE id = 1;
```

Con Eloquent y un Modelo, escribiremos esto (PHP puro):
```php
$post = Post::find(1);
```

## 🛠️ 1. Creando un Modelo

Para crear un modelo, usamos de nuevo a nuestro fiel asistente Artisan. En tu terminal ejecuta:

```bash
php artisan make:model Post
```

Esto creará un archivo llamado `Post.php` en la carpeta `app/Models/`.

> **💡 Pro-Tip (El combo de los profesionales):** > Casi siempre que creas un modelo, también necesitas su tabla. Puedes crear ambas cosas al mismo tiempo añadiendo la bandera `-m`:
> `php artisan make:model Producto -m` (Esto crea el Modelo y su Migración de un solo golpe).

## 📏 2. Las Leyes de Nomenclatura (Naming Conventions)

Si abres `app/Models/Post.php`, verás que la clase está casi vacía. ¿Cómo sabe Laravel a qué tabla debe conectarse si no se lo hemos dicho? 

Laravel es increíblemente inteligente y asume las cosas basándose en **el idioma inglés**:

1. **El Modelo** siempre va en **Singular y PascalCase**. (ej. `Post`, `User`, `Category`).
2. **La Tabla** siempre debe ir en **Plural y snake_case**. (ej. `posts`, `users`, `categories`).

Como llamaste a tu modelo `Post`, Laravel busca automáticamente en la base de datos una tabla llamada `posts`. Si tus nombres respetan esta regla, ¡no tienes que configurar nada más!

*Excepción: Si por alguna razón tu tabla se llama distinto (ej. `mis_articulos`), puedes forzar la conexión añadiendo esto dentro de tu modelo:*
```php
protected $table = 'mis_articulos';
```

## 🔐 3. Preparando el Modelo para guardar datos

Antes de poder insertar datos masivamente en nuestra tabla, hay una medida de seguridad vital en Laravel llamada **Asignación Masiva** (*Mass Assignment*).

Por defecto, Laravel protege tus tablas para que un usuario malintencionado no pueda inyectar datos en columnas protegidas (como `is_admin`). Debemos decirle al modelo qué columnas sí está permitido rellenar.

Abre tu `app/Models/Post.php` y añade la propiedad `$fillable`:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use HasFactory;

    // Le decimos a Laravel: "Está bien, puedes guardar datos en estas columnas"
    protected $fillable = [
        'titulo',
        'contenido',
        'esta_publicado'
    ];
}
```

---

### 🚀 Siguiente paso
Ya tenemos la tabla (Migración) y el mensajero (Modelo). Pero probar nuestra aplicación con una base de datos vacía es muy tedioso. En lugar de crear 50 posts a mano uno por uno, en el siguiente tema descubriremos el secreto de los profesionales: **Factories y Seeders**, para poblar nuestra base de datos en 2 segundos.