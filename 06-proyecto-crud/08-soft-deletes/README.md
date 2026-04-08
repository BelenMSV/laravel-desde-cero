# Tema 8: Soft Deletes (La Papelera de Reciclaje)

¿Te has preguntado por qué en las aplicaciones grandes casi nunca pierdes tu información, incluso si le das a "Eliminar"? Esto se debe a que no borran el registro de la base de datos, simplemente lo ocultan.

Laravel incluye un sistema llamado **Soft Deletes**. En lugar de usar el comando `DELETE` de SQL, Laravel añade una columna llamada `deleted_at` con la fecha en la que "borraste" el producto. A partir de ese momento, Laravel lo oculta de todas tus consultas automáticamente.

## 🛠️ 1. Modificando la Base de Datos

Como nuestra tabla `productos` ya existe, vamos a crear una nueva migración solo para añadirle esta columna especial.

Abre tu terminal y ejecuta:
```bash
php artisan make:migration add_deleted_at_to_productos_table
```

Abre el nuevo archivo en `database/migrations/` y modifícalo así:

```php
public function up(): void
{
    Schema::table('productos', function (Blueprint $table) {
        // Esto añade la columna 'deleted_at'
        $table->softDeletes(); 
    });
}

public function down(): void
{
    Schema::table('productos', function (Blueprint $table) {
        $table->dropSoftDeletes();
    });
}
```

Ejecuta la migración:
```bash
php artisan migrate
```

## 🧱 2. Activando el "Poder" en el Modelo

Para que Laravel sepa que este modelo usa borrado lógico, debemos añadirle un **Trait** (una porción de código reutilizable). 

Abre `app/Models/Producto.php`:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
// 1. Importamos la clase
use Illuminate\Database\Eloquent\SoftDeletes; 

class Producto extends Model
{
    // 2. Añadimos el Trait dentro de la clase
    use HasFactory, SoftDeletes; 

    protected $fillable = [
        'categoria_id', 'nombre', 'descripcion', 
        'precio', 'stock', 'imagen_url'
    ];

    public function categoria()
    {
        return $this->belongsTo(Categoria::class);
    }
}
```

¡Ya está! No tienes que cambiar tu Controlador (`destroy()`). Ahora, cuando llames a `$producto->delete()`, Laravel no lo borrará de verdad, solo rellenará la fecha `deleted_at`. Pruébalo borrando un producto en tu web: desaparecerá del catálogo, pero si miras tu base de datos con un gestor, seguirá ahí.

## 🛣️ 3. Rutas para la Papelera

Ahora necesitamos una forma de ver esos productos borrados y restaurarlos. Vamos a `routes/web.php` y añadimos estas rutas **JUSTO ANTES** de nuestro `Route::resource`.

```php
// Rutas de la Papelera (Deben ir antes del resource para no causar conflictos)
Route::get('/productos-papelera', [ProductoController::class, 'papelera'])->name('productos.papelera');
Route::post('/productos/{id}/restaurar', [ProductoController::class, 'restaurar'])->name('productos.restaurar');
Route::delete('/productos/{id}/forzar-eliminacion', [ProductoController::class, 'forzarEliminacion'])->name('productos.forzar');

// Nuestro CRUD original
Route::resource('productos', ProductoController::class);
```

## 🧠 4. Lógica en el Controlador

Abre tu `ProductoController.php` y añade estos tres nuevos métodos al final:

```php
use Illuminate\Support\Facades\Storage; // Asegúrate de tener esto

// 1. Mostrar los productos borrados
public function papelera()
{
    // onlyTrashed() trae SOLO los que tienen una fecha en deleted_at
    $productos = Producto::onlyTrashed()->with('categoria')->paginate(10);
    return view('productos.papelera', compact('productos'));
}

// 2. Restaurar un producto
public function restaurar($id)
{
    // Buscamos el producto en la papelera
    $producto = Producto::onlyTrashed()->findOrFail($id);
    
    // Lo restauramos (Pone deleted_at en NULL)
    $producto->restore();

    return redirect()->route('productos.papelera')
        ->with('success', 'Producto restaurado con éxito.');
}

// 3. Borrar permanentemente (Esta vez de verdad)
public function forzarEliminacion($id)
{
    $producto = Producto::onlyTrashed()->findOrFail($id);

    // Borramos la imagen del disco duro
    if ($producto->imagen_url) {
        Storage::disk('public')->delete($producto->imagen_url);
    }

    // forceDelete() lo destruye de la base de datos para siempre
    $producto->forceDelete();

    return redirect()->route('productos.papelera')
        ->with('success', 'Producto destruido permanentemente.');
}
```

## ♻️ 5. La Vista de la Papelera

Crea un archivo llamado `papelera.blade.php` en `resources/views/productos/`. Es muy similar al listado normal, pero con botones diferentes:

```html
@extends('layouts.app')

@section('title', 'Papelera de Reciclaje')

@section('content')
    <div class="flex justify-between items-center mb-6">
        <h1 class="text-3xl font-bold text-gray-800">Papelera de Reciclaje</h1>
        <a href="{{ route('productos.index') }}" class="bg-blue-600 text-white px-4 py-2 rounded">Volver al Catálogo</a>
    </div>

    @if($productos->count() > 0)
        <table class="w-full bg-white shadow-md rounded mb-4">
            <thead>
                <tr class="bg-gray-200 text-gray-600 uppercase text-sm leading-normal">
                    <th class="py-3 px-6 text-left">Producto</th>
                    <th class="py-3 px-6 text-left">Borrado el</th>
                    <th class="py-3 px-6 text-center">Acciones</th>
                </tr>
            </thead>
            <tbody class="text-gray-600 text-sm font-light">
                @foreach($productos as $producto)
                    <tr class="border-b border-gray-200 hover:bg-gray-100">
                        <td class="py-3 px-6 text-left whitespace-nowrap font-bold">{{ $producto->nombre }}</td>
                        <td class="py-3 px-6 text-left">{{ $producto->deleted_at->diffForHumans() }}</td>
                        <td class="py-3 px-6 text-center flex justify-center gap-2">
                            
                            <form action="{{ route('productos.restaurar', $producto->id) }}" method="POST">
                                @csrf
                                <button type="submit" class="bg-green-500 text-white px-3 py-1 rounded text-sm hover:bg-green-600">Restaurar</button>
                            </form>

                            <form action="{{ route('productos.forzar', $producto->id) }}" method="POST" onsubmit="return confirm('¿Destruir para siempre? Esta acción no se puede deshacer.');">
                                @csrf
                                @method('DELETE')
                                <button type="submit" class="bg-red-500 text-white px-3 py-1 rounded text-sm hover:bg-red-600">Destruir</button>
                            </form>

                        </td>
                    </tr>
                @endforeach
            </tbody>
        </table>
        {{ $productos->links() }}
    @else
        <div class="bg-blue-100 text-blue-700 p-4 rounded">
            La papelera está vacía.
        </div>
    @endif
@endsection
```

*Por último, no olvides añadir un pequeño botón en tu `index.blade.php` o en el menú de navegación (en `app.blade.php`) que lleve a `{{ route('productos.papelera') }}` para que el usuario pueda acceder a ella.*

---

### 🎉 ¡MÓDULO 6 COMPLETADO!
Has construido una aplicación de gestión empresarial de principio a fin. Sabes diseñar bases de datos, generar datos de prueba, programar controladores, proteger peticiones, manejar archivos, interceptar búsquedas y borrar datos de forma segura.

Con este conocimiento, ya eres capaz de construir el 80% de las aplicaciones web que existen en el mercado. En el próximo y último **Módulo 7**, daremos el salto de nivel Junior a nivel Intermedio-Avanzado, aprendiendo a automatizar correos y programar tareas que se ejecuten solas en segundo plano.