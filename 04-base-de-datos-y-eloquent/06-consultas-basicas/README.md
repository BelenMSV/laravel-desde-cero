# Tema 6: Consultas Básicas (El CRUD con Eloquent)

Ya tenemos datos en nuestra base de datos. Ahora necesitamos extraerlos para mostrarlos en nuestras vistas, o modificarlos según las acciones del usuario. 

Con **Eloquent ORM**, no necesitas saber SQL complejo. Laravel utiliza métodos de PHP súper intuitivos que hacen que interactuar con la base de datos sea casi como hablar en inglés. A estas operaciones básicas se les conoce como **CRUD** (Create, Read, Update, Delete).



Veamos cómo hacer cada una de estas operaciones directamente desde un Controlador (o desde Tinker).

## 📖 1. Leer datos (Read)

Es la operación más común. Tienes múltiples formas de buscar información usando tu Modelo `Post`:

```php
use App\Models\Post;

// Traer TODOS los posts de la tabla
$todos = Post::all();

// Buscar un post específico por su ID (ej. el post número 5)
$post = Post::find(5);

// Buscar el primer post que cumpla una condición
$postPublicado = Post::where('esta_publicado', true)->first();

// Traer VARIOS posts que cumplan una condición (devuelve una colección/arreglo)
$postsFiltrados = Post::where('esta_publicado', true)->get();

// Traer posts ordenados y con un límite (Ideal para los "Últimos 3 artículos")
$ultimos = Post::orderBy('created_at', 'desc')->take(3)->get();
```

## ✍️ 2. Crear datos (Create)

Para crear un nuevo registro, utilizamos el método `create()`. Le pasamos un arreglo con los datos que queremos guardar. 

*⚠️ **Recuerda:** Para que este método funcione, las columnas deben estar declaradas en la propiedad `$fillable` de tu Modelo, tal y como vimos en el Tema 3.*

```php
$nuevoPost = Post::create([
    'titulo' => 'Aprender Laravel es genial',
    'contenido' => 'Este framework me ahorra muchísimo tiempo...',
    'esta_publicado' => true
]);
```

## 🔄 3. Actualizar datos (Update)

Actualizar un registro es un proceso de dos pasos: primero lo buscas, luego cambias sus valores y finalmente guardas los cambios.

**Método 1: Buscar, Modificar y Guardar (`save`)**
```php
// 1. Buscamos el post
$post = Post::find(1);

// 2. Modificamos la propiedad como si fuera un objeto normal
$post->titulo = 'Título Actualizado';

// 3. Guardamos los cambios en la base de datos
$post->save();
```

**Método 2: Actualización Masiva (`update`)**
```php
$post = Post::find(1);

// Actualiza directamente usando un arreglo
$post->update([
    'titulo' => 'Otro título nuevo',
    'esta_publicado' => false
]);
```

## 🗑️ 4. Borrar datos (Delete)

Borrar un registro es igual de sencillo. Solo necesitas encontrarlo y aplicar el método `delete()`.

```php
$post = Post::find(10); // Buscamos el post 10

if ($post) {
    $post->delete(); // ¡Adiós post!
}
```

También puedes borrar un registro directamente si conoces su ID, sin tener que buscarlo primero:

```php
Post::destroy(10); // Borra el post con ID 10 al instante
```

---

### 🚀 Siguiente paso
Con esto ya sabes cómo manejar toda la información de una tabla de forma independiente. Sin embargo, en la vida real las tablas casi nunca están solas; están conectadas. Un "Usuario" tiene "Posts", y un "Post" tiene "Comentarios". En nuestro último tema de este módulo, daremos el salto al nivel avanzado: **Las Relaciones de Eloquent**.