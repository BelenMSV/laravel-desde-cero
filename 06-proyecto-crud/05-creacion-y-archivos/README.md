# Tema 5: Formulario de Creación y Subida de Imágenes

Añadir un producto nuevo no es solo guardar texto en la base de datos; necesitamos asegurarnos de que el precio sea un número, que el usuario haya seleccionado una categoría válida y, por supuesto, procesar la imagen del producto.

## 🛡️ 1. El Form Request (Validación)

Como aprendimos en el Módulo 5, no vamos a ensuciar nuestro Controlador con reglas de validación. Vamos a crear un archivo dedicado para esto:

```bash
php artisan make:request StoreProductoRequest
```

Abre `app/Http/Requests/StoreProductoRequest.php` y configura las reglas:

```php
public function authorize(): bool
{
    return true; // Permitimos que cualquiera use el formulario por ahora
}

public function rules(): array
{
    return [
        'categoria_id' => 'required|exists:categorias,id', // Debe existir en la tabla categorías
        'nombre' => 'required|string|min:3|max:255',
        'descripcion' => 'required|string',
        'precio' => 'required|numeric|min:0',
        'stock' => 'required|integer|min:0',
        // La imagen es opcional, pero si suben algo, DEBE ser imagen y máximo 2MB
        'imagen' => 'nullable|image|mimes:jpeg,png,jpg,webp|max:2048', 
    ];
}
```

## 🧠 2. La Lógica del Controlador

Abre tu `ProductoController.php` y vamos a modificar dos métodos: `create` (para mostrar el formulario) y `store` (para guardar los datos).

**Importante:** Recuerda importar tu nuevo Request y el modelo Categoría en la parte superior.

```php
use App\Models\Producto;
use App\Models\Categoria;
use App\Http\Requests\StoreProductoRequest;

// 1. Mostrar el formulario
public function create()
{
    // Necesitamos enviarle a la vista todas las categorías para llenar el <select>
    $categorias = Categoria::all();
    return view('productos.create', compact('categorias'));
}

// 2. Guardar el producto
public function store(StoreProductoRequest $request)
{
    // Si el código llega aquí, la validación ya pasó con éxito
    $datos = $request->validated();

    // Verificamos si el usuario subió una imagen
    if ($request->hasFile('imagen')) {
        // Guardamos la imagen en storage/app/public/productos
        $ruta = $request->file('imagen')->store('productos', 'public');
        $datos['imagen_url'] = $ruta; // Añadimos la ruta a nuestros datos validados
    }

    // Creamos el producto en la base de datos
    Producto::create($datos);

    // Redirigimos al listado con un mensaje de éxito
    return redirect()->route('productos.index')
        ->with('success', '¡Producto creado correctamente!');
}
```

## 🎨 3. La Vista del Formulario (`create.blade.php`)

Crea el archivo en `resources/views/productos/create.blade.php`. Presta especial atención al `enctype` para las imágenes y a cómo usamos `old()` para que el usuario no pierda sus datos si se equivoca.

```html
@extends('layouts.app')

@section('title', 'Nuevo Producto')

@section('content')
    <div class="max-w-2xl mx-auto bg-white p-8 rounded-lg shadow-md">
        <h1 class="text-2xl font-bold mb-6 text-gray-800">Añadir Nuevo Producto</h1>

        <form action="{{ route('productos.store') }}" method="POST" enctype="multipart/form-data">
            @csrf

            <div class="mb-4">
                <label class="block text-gray-700 font-bold mb-2">Categoría</label>
                <select name="categoria_id" class="w-full border p-2 rounded focus:outline-none focus:ring-2 focus:ring-blue-500">
                    <option value="">Selecciona una categoría...</option>
                    @foreach($categorias as $categoria)
                        <option value="{{ $categoria->id }}" {{ old('categoria_id') == $categoria->id ? 'selected' : '' }}>
                            {{ $categoria->nombre }}
                        </option>
                    @endforeach
                </select>
                @error('categoria_id') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 font-bold mb-2">Nombre del Producto</label>
                <input type="text" name="nombre" value="{{ old('nombre') }}" class="w-full border p-2 rounded">
                @error('nombre') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
            </div>

            <div class="mb-4">
                <label class="block text-gray-700 font-bold mb-2">Descripción</label>
                <textarea name="descripcion" rows="4" class="w-full border p-2 rounded">{{ old('descripcion') }}</textarea>
                @error('descripcion') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
            </div>

            <div class="grid grid-cols-2 gap-4 mb-4">
                <div>
                    <label class="block text-gray-700 font-bold mb-2">Precio ($)</label>
                    <input type="number" step="0.01" name="precio" value="{{ old('precio') }}" class="w-full border p-2 rounded">
                    @error('precio') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>
                <div>
                    <label class="block text-gray-700 font-bold mb-2">Stock (Cant.)</label>
                    <input type="number" name="stock" value="{{ old('stock') }}" class="w-full border p-2 rounded">
                    @error('stock') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>
            </div>

            <div class="mb-6">
                <label class="block text-gray-700 font-bold mb-2">Fotografía (Opcional)</label>
                <input type="file" name="imagen" accept="image/*" class="w-full border p-2 rounded bg-gray-50">
                @error('imagen') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
            </div>

            <div class="flex justify-end gap-4">
                <a href="{{ route('productos.index') }}" class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600">Cancelar</a>
                <button type="submit" class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 font-bold">Guardar Producto</button>
            </div>
        </form>
    </div>
@endsection
```

## 🖼️ 4. Actualizando la vista de Listado (`index.blade.php`)

¿Recuerdas que en el Tema 4 dejamos un espacio gris para la imagen? Ahora que podemos subir imágenes, vamos a actualizar ese trozo de código en `resources/views/productos/index.blade.php`.

Reemplaza el `div` gris de la imagen por esto:

```html
@if($producto->imagen_url)
    <img src="{{ asset('storage/' . $producto->imagen_url) }}" alt="{{ $producto->nombre }}" class="w-full h-48 object-cover">
@else
    <div class="h-48 bg-gray-200 flex items-center justify-center">
        <span class="text-gray-400">Sin Imagen</span>
    </div>
@endif
```

*(Nota: Asegúrate de haber ejecutado `php artisan storage:link` como vimos en el Módulo 5, o las imágenes aparecerán rotas).*

## 🔔 5. Bonus: Mostrar el mensaje de éxito

En el Controlador (`store`), enviamos un mensaje a la sesión (`with('success', '...')`). Para que el usuario lo vea, vamos a añadir una alerta en nuestro layout base.

Abre `resources/views/layouts/app.blade.php` y pon esto justo debajo del `<main>` y antes del `@yield('content')`:

```html
@if(session('success'))
    <div class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded relative mb-4" role="alert">
        <strong class="font-bold">¡Éxito!</strong>
        <span class="block sm:inline">{{ session('success') }}</span>
    </div>
@endif
```

---

### 🚀 Siguiente paso
¡Felicidades! Ya tienes la "C" (Create) y la "R" (Read) de tu CRUD completamente funcionales a nivel profesional. En el próximo tema cerraremos el ciclo estándar con el **Tema 6: Edición y Eliminación (Update & Delete)**, aprendiendo a pre-cargar datos en un formulario y a borrar registros de forma segura.