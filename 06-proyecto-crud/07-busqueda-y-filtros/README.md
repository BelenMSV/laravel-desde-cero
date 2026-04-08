# Tema 7: Barra de Búsqueda y Filtros

En el mundo real, los usuarios no navegan página por página; usan el buscador. Vamos a implementar una barra de búsqueda para que los usuarios puedan encontrar productos por su nombre o descripción.

Esto nos enseñará a usar formularios con el método `GET` (que ponen los datos en la URL) y a modificar nuestras consultas de Eloquent dinámicamente.

## 🔍 1. El Formulario de Búsqueda (Método GET)

Abre tu vista principal `resources/views/productos/index.blade.php`. Vamos a colocar una barra de búsqueda justo encima de nuestra cuadrícula de productos.

**Presta mucha atención:** Este formulario *no* usa `@csrf` y su método es `GET`, no `POST`.

```html
@extends('layouts.app')

@section('title', 'Listado de Productos')

@section('content')
    <div class="flex justify-between items-center mb-6">
        <h1 class="text-3xl font-bold text-gray-800">Nuestros Productos</h1>

        <form action="{{ route('productos.index') }}" method="GET" class="flex gap-2">
            <input 
                type="text" 
                name="buscar" 
                placeholder="Buscar producto..." 
                value="{{ request('buscar') }}" 
                class="border border-gray-300 rounded px-4 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
            <button type="submit" class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">Buscar</button>
            
            @if(request('buscar'))
                <a href="{{ route('productos.index') }}" class="bg-gray-300 text-gray-700 px-4 py-2 rounded hover:bg-gray-400">Limpiar</a>
            @endif
        </form>
    </div>

    ```

Si pruebas esto ahora y buscas "Camiseta", verás que la URL de tu navegador cambia a `http://localhost:8000/productos?buscar=Camiseta`. ¡Perfecto! Ahora tenemos que decirle a Laravel que lea ese `?buscar=`.

## 🧠 2. Interceptando la búsqueda en el Controlador

Abre tu `ProductoController.php` y dirígete al método `index`. Vamos a cambiarlo para que reaccione si viene un texto de búsqueda.

Tendremos que cambiar nuestro modelo de inyectar el `Request` (recuerda importarlo arriba si no lo tienes: `use Illuminate\Http\Request;`).

```php
public function index(Request $request)
{
    // 1. Iniciamos la consulta base (con su relación para no perder rendimiento)
    $query = Producto::with('categoria');

    // 2. Si el usuario escribió algo en el buscador...
    if ($request->has('buscar') && $request->buscar != '') {
        $termino = $request->buscar;
        
        // Modificamos la consulta usando LIKE
        // Los % significan "cualquier texto antes o después"
        $query->where('nombre', 'LIKE', '%' . $termino . '%')
              ->orWhere('descripcion', 'LIKE', '%' . $termino . '%');
    }

    // 3. Finalmente, ejecutamos la consulta y paginamos
    $productos = $query->paginate(10);

    return view('productos.index', compact('productos'));
}
```

## ⚠️ 3. El Problema de la Paginación (y su mágica solución)

Pruébalo. Busca algo genérico que devuelva muchos resultados (por ejemplo, la letra "a"). Verás que funciona y te devuelve, digamos, 3 páginas de resultados.

Pero si haces clic en el botón "Página 2" en la parte inferior de tu pantalla, **la búsqueda se romperá y volverás a ver todos los productos**. 

¿Por qué? Porque el enlace de la página 2 generado por Laravel es `?page=2`, y se olvidó del `?buscar=a`.

Solucionar esto en PHP puro tomaría decenas de líneas de código. En Laravel 8+, se soluciona añadiendo **una sola palabra** en el controlador.

Regresa a tu `ProductoController.php` y añade `->withQueryString()` al final de tu paginación:

```php
    // Mantendrá ?buscar=texto en todos los botones de paginación
    $productos = $query->paginate(10)->withQueryString(); 
```

¡Eso es todo! Ahora la URL de tu página 2 será `?buscar=a&page=2`. Tu búsqueda ahora es a prueba de balas.

---

### 🚀 Siguiente paso
Nuestra tienda es ahora completamente funcional, navegable y buscable. Pero nos falta un pequeño detalle de nivel empresarial: ¿Qué pasa si un empleado borra por accidente la "Laptop" más vendida del sistema? ¡Perderemos todo su historial de ventas! 

En el **Tema 8**, implementaremos los **Soft Deletes (Borrados Lógicos)**, una técnica avanzada que crea una "Papelera de reciclaje" invisible en tu base de datos.