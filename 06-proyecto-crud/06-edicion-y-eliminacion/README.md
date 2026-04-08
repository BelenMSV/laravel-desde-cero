# Tema 6: Edición y Eliminación (Update & Delete)

Con este tema completaremos las 4 operaciones fundamentales de cualquier sistema: Crear, Leer, Actualizar y Borrar. Además, aprenderemos una regla de oro del desarrollo backend: **Si borras o actualizas un registro que tiene una imagen, también debes borrar el archivo antiguo del disco** para no saturar tu servidor.

## 🛡️ 1. Validación para la Actualización

Vamos a crear un Form Request específico para la actualización. 

```bash
php artisan make:request UpdateProductoRequest
```

Abre `app/Http/Requests/UpdateProductoRequest.php`. Las reglas son casi idénticas a las de creación, pero la imagen siempre es opcional al editar.

```php
public function authorize(): bool
{
    return true;
}

public function rules(): array
{
    return [
        'categoria_id' => 'required|exists:categorias,id',
        'nombre' => 'required|string|min:3|max:255',
        'descripcion' => 'required|string',
        'precio' => 'required|numeric|min:0',
        'stock' => 'required|integer|min:0',
        // Opcional: Solo validamos si el usuario decide cambiar la foto
        'imagen' => 'nullable|image|mimes:jpeg,png,jpg,webp|max:2048', 
    ];
}
```

## 🔄 2. Lógica de Edición en el Controlador

Abre `ProductoController.php`. Recuerda importar la clase `Storage` en la parte superior (`use Illuminate\Support\Facades\Storage;`) y tu nuevo `UpdateProductoRequest`.

Vamos a rellenar los métodos `edit` y `update`:

```php
// 1. Mostrar el formulario de edición
public function edit(Producto $producto)
{
    $categorias = Categoria::all();
    // Pasamos el producto específico y las categorías a la vista
    return view('productos.edit', compact('producto', 'categorias'));
}

// 2. Guardar los cambios
public function update(UpdateProductoRequest $request, Producto $producto)
{
    $datos = $request->validated();

    // Si el usuario subió una nueva imagen...
    if ($request->hasFile('imagen')) {
        // A. Borramos la imagen antigua del disco (si existía)
        if ($producto->imagen_url) {
            Storage::disk('public')->delete($producto->imagen_url);
        }
        // B. Guardamos la nueva imagen
        $ruta = $request->file('imagen')->store('productos', 'public');
        $datos['imagen_url'] = $ruta;
    }

    // Actualizamos el registro en la base de datos
    $producto->update($datos);

    return redirect()->route('productos.index')
        ->with('success', '¡Producto actualizado correctamente!');
}
```

## ✏️ 3. La Vista de Edición (`edit.blade.php`)

Crea el archivo `resources/views/productos/edit.blade.php`. Fíjate en dos cosas clave: la directiva `@method('PUT')` y cómo usamos `old('campo', $producto->campo)` para mostrar lo que el usuario acaba de escribir si hay error, o el valor de la base de datos por defecto.

```html
@extends('layouts.app')

@section('title', 'Editar Producto')

@section('content')
    <div class="max-w-2xl mx-auto bg-white p-8 rounded-lg shadow-md">
        <h1 class="text-2xl font-bold mb-6 text-gray-800">Editar: {{ $producto->nombre }}</h1>

        <form action="{{ route('productos.update', $producto) }}" method="POST" enctype="multipart/form-data">
            @csrf
            @method('PUT')

            <div class="mb-4">
                <label class="block text-gray-700 font-bold mb-2">Categoría</label>
                <select name="categoria_id" class="w-full border p-2 rounded">
                    @foreach($categorias as $categoria)
                        <option value="{{ $categoria->id }}" {{ old('categoria_id', $producto->categoria_id) == $categoria->id ? 'selected' : '' }}>
                            {{ $categoria->nombre }}
                        </option>
                    @endforeach
                </select>
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 font-bold mb-2">Nombre</label>
                <input type="text" name="nombre" value="{{ old('nombre', $producto->nombre) }}" class="w-full border p-2 rounded">
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 font-bold mb-2">Descripción</label>
                <textarea name="descripcion" rows="4" class="w-full border p-2 rounded">{{ old('descripcion', $producto->descripcion) }}</textarea>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-4">
                <div>
                    <label class="block text-gray-700 font-bold mb-2">Precio ($)</label>
                    <input type="number" step="0.01" name="precio" value="{{ old('precio', $producto->precio) }}" class="w-full border p-2 rounded">
                </div>
                <div>
                    <label class="block text-gray-700 font-bold mb-2">Stock</label>
                    <input type="number" name="stock" value="{{ old('stock', $producto->stock) }}" class="w-full border p-2 rounded">
                </div>
            </div>

            <div class="mb-6">
                <label class="block text-gray-700 font-bold mb-2">Cambiar Fotografía (Opcional)</label>
                @if($producto->imagen_url)
                    <div class="mb-2">
                        <img src="{{ asset('storage/' . $producto->imagen_url) }}" alt="Actual" class="w-32 h-32 object-cover rounded">
                    </div>
                @endif
                <input type="file" name="imagen" accept="image/*" class="w-full border p-2 rounded bg-gray-50">
            </div>

            <div class="flex justify-end gap-4">
                <a href="{{ route('productos.index') }}" class="bg-gray-500 text-white px-4 py-2 rounded">Cancelar</a>
                <button type="submit" class="bg-yellow-500 text-white px-4 py-2 rounded font-bold hover:bg-yellow-600">Actualizar Producto</button>
            </div>
        </form>
    </div>
@endsection
```

## 🗑️ 4. Eliminación en el Controlador (Destroy)

De vuelta en `ProductoController.php`, añadimos el código para borrar. Igual que en la edición, debemos limpiar el disco primero.

```php
public function destroy(Producto $producto)
{
    // 1. Borramos la imagen física si existe
    if ($producto->imagen_url) {
        Storage::disk('public')->delete($producto->imagen_url);
    }

    // 2. Borramos el registro de la base de datos
    $producto->delete();

    return redirect()->route('productos.index')
        ->with('success', '¡Producto eliminado con éxito!');
}
```

## 🧨 5. El Botón de Borrar (Spoofing de DELETE)

Finalmente, abre tu vista del listado (`resources/views/productos/index.blade.php`). Vamos a añadir el botón de borrar justo al lado del botón de editar que pusimos en el Tema 4.

Recuerda: **Un botón de borrar NUNCA debe ser un simple enlace `<a>`**. Debe ser un formulario POST con la directiva `@method('DELETE')`.

Busca el bloque de botones y déjalo así:

```html
<div class="mt-4 flex gap-2">
    <a href="{{ route('productos.edit', $producto) }}" class="bg-yellow-400 text-yellow-900 px-3 py-1 rounded text-sm hover:bg-yellow-300">Editar</a>
    
    <form action="{{ route('productos.destroy', $producto) }}" method="POST" onsubmit="return confirm('¿Estás seguro de que deseas eliminar este producto?');">
        @csrf
        @method('DELETE')
        <button type="submit" class="bg-red-500 text-white px-3 py-1 rounded text-sm hover:bg-red-600">Borrar</button>
    </form>
</div>
```
*(El `onsubmit="return confirm(...)"` es un pequeño truco de JavaScript para mostrar una alerta de confirmación antes de borrar).*

---

### 🎉 ¡CRUD Completado!
Has construido un sistema de gestión completo. Eres capaz de crear, listar, actualizar y eliminar registros relacionados en una base de datos, manejando la subida de archivos físicos de forma segura. 

En muchos cursos, la historia terminaría aquí. Pero nosotros vamos a llevar esta aplicación al estándar de la industria. En el **Tema 7**, añadiremos una **Barra de Búsqueda y Filtros** para que nuestro catálogo sea útil en el mundo real.