# Tema 4: Layout Base, Listado y Paginación

Hasta ahora, si quisiéramos crear 5 páginas, tendríamos que copiar y pegar el mismo HTML del menú y el pie de página en cada archivo. Esto es un error grave de mantenimiento. En Laravel, usamos **Layouts** para definir una estructura común.

## 🏗️ 1. El Layout Base (`app.blade.php`)

Crea una carpeta en `resources/views/layouts/` y dentro un archivo llamado `app.blade.php`. Usaremos un CDN de Tailwind CSS para que se vea moderno sin tener que instalar ni configurar nada extra.

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Catálogo Laravel - @yield('title')</title>
    <script src="[https://cdn.tailwindcss.com](https://cdn.tailwindcss.com)"></script>
</head>
<body class="bg-gray-100">
    <nav class="bg-blue-600 p-4 text-white shadow-lg">
        <div class="container mx-auto flex justify-between items-center">
            <a href="{{ route('productos.index') }}" class="font-bold text-xl">Mi Tienda</a>
            <a href="{{ route('productos.create') }}" class="bg-blue-500 px-4 py-2 rounded hover:bg-blue-400 font-semibold transition"> + Nuevo Producto</a>
        </div>
    </nav>

    <main class="container mx-auto mt-10 px-4">
        @yield('content') 
    </main>
</body>
</html>
```

## 🧠 2. Lógica del Controlador: La Paginación

Si tenemos 1,000 productos, no podemos mostrarlos todos de golpe porque la página tardaría una eternidad en cargar y el usuario tendría que hacer scroll infinito. Laravel soluciona esto de forma brillante con el método `paginate()`.

Abre `app/Http/Controllers/ProductoController.php` y actualiza el método `index`:

```php
public function index()
{
    // En lugar de all(), usamos paginate(10). 
    // Esto traerá solo 10 productos y detectará automáticamente la página actual leyendo la URL (ej: ?page=2)
    // Usamos with('categoria') para optimizar la consulta (Eager Loading)
    $productos = Producto::with('categoria')->paginate(10);

    return view('productos.index', compact('productos'));
}
```

## 🎨 3. La Vista de Listado (`index.blade.php`)

Crea el archivo en `resources/views/productos/index.blade.php`. Observa cómo usamos la directiva `@extends` para heredar el layout que acabamos de crear y `@section` para inyectar el contenido.

```html
@extends('layouts.app')

@section('title', 'Listado de Productos')

@section('content')
    <h1 class="text-3xl font-bold mb-6 text-gray-800">Nuestros Productos</h1>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        @foreach ($productos as $producto)
            <div class="bg-white rounded-lg shadow-md overflow-hidden hover:shadow-xl transition-shadow duration-300">
                
                <div class="h-48 bg-gray-200 flex items-center justify-center">
                    <span class="text-gray-400">Sin Imagen</span>
                </div>

                <div class="p-4">
                    <span class="text-xs font-semibold text-blue-600 uppercase">{{ $producto->categoria->nombre }}</span>
                    <h2 class="text-xl font-bold mt-2 text-gray-800">{{ $producto->nombre }}</h2>
                    <p class="text-gray-600 mt-2 text-sm">{{ Str::limit($producto->descripcion, 100) }}</p>
                    
                    <div class="mt-4 flex justify-between items-center">
                        <span class="text-2xl font-bold text-gray-900">${{ $producto->precio }}</span>
                        <span class="text-sm font-medium {{ $producto->stock > 0 ? 'text-green-500' : 'text-red-500' }}">
                            Stock: {{ $producto->stock }}
                        </span>
                    </div>

                    <div class="mt-4 flex gap-2">
                        <a href="{{ route('productos.edit', $producto) }}" class="bg-yellow-400 text-yellow-900 px-3 py-1 rounded text-sm hover:bg-yellow-300">Editar</a>
                    </div>
                </div>
            </div>
        @endforeach
    </div>

    <div class="mt-8 mb-12">
        {{ $productos->links() }}
    </div>
@endsection
```

### 🧐 ¿Qué hace exactamente `links()`?
La directiva `{{ $productos->links() }}` lee la información del paginador y genera automáticamente todo el código HTML necesario para los botones de "Anterior", "Siguiente" y los números de página. Además, Laravel 11 ya viene configurado por defecto para que estos botones usen las clases de Tailwind CSS, por lo que se verán perfectos sin que tengas que escribir nada de CSS.

---

### 🚀 Siguiente paso
Nuestra tienda ya muestra los productos de forma elegante y paginada, ¡pero todavía no podemos añadir nuevos desde la interfaz! En el **Tema 5**, aprenderemos a crear el formulario de creación, a validar los datos usando Form Requests y, lo más emocionante, a gestionar la **Subida de Archivos** para que nuestros productos tengan fotos reales.