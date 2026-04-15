# Tema 1: Eloquent Avanzado (Consultas de Reporte)

Hasta ahora, nuestras consultas a la base de datos han sido muy sencillas: "Tráeme todos los productos" o "Búscame el producto con ID 5". Pero imagina que el gerente de la tienda te pide el siguiente reporte:

* *"¿Cuál es el valor total en dinero de todo nuestro inventario?"*
* *"¿Cuántos productos tenemos con el stock a cero?"*
* *"¿Cuántos productos hay dentro de cada categoría?"*

Traer todos los productos con `Producto::all()` y sumarlos usando un bucle `foreach` en PHP es un error gravísimo que consumirá toda la memoria de tu servidor. Para esto, debemos usar **Funciones de Agregación** directamente en la base de datos.

## 📊 1. Preparando una Ruta de Pruebas

Antes de meter estas consultas en un correo o en un comando automático, vamos a crear una ruta temporal en `routes/web.php` solo para imprimir los resultados en la pantalla y asegurarnos de que nuestras matemáticas funcionan.

Abre `routes/web.php` y añade esto al final:

```php
use App\Models\Producto;
use App\Models\Categoria;

Route::get('/laboratorio-reporte', function () {
    // Aquí haremos nuestros experimentos
    return "Laboratorio listo";
});
```

## 🧮 2. Funciones de Agregación Básicas

Eloquent nos permite traducir comandos SQL matemáticos (`SUM`, `COUNT`, `MAX`) de forma muy elegante. Vamos a calcular nuestros dos primeros indicadores dentro de nuestra ruta de laboratorio:

```php
Route::get('/laboratorio-reporte', function () {
    
    // 1. Contar productos sin stock
    // Equivalente SQL: SELECT COUNT(*) FROM productos WHERE stock = 0;
    $productosAgotados = Producto::where('stock', 0)->count();

    // 2. Calcular el valor total del inventario
    // Multiplicamos el precio por el stock de cada producto y sumamos todo
    // Equivalente SQL: SELECT SUM(precio * stock) FROM productos;
    $valorTotalInventario = Producto::selectRaw('SUM(precio * stock) as total')->value('total');

    return [
        'Agotados' => $productosAgotados,
        'Valor Total ($)' => number_format($valorTotalInventario, 2)
    ];
});
```
*Si visitas `http://localhost:8000/laboratorio-reporte` en tu navegador, verás un JSON con tus cálculos exactos hechos a la velocidad de la luz.*

## 🧩 3. Consultas Complejas: Agrupar Resultados (GroupBy)

Ahora vamos a resolver la pregunta más difícil: *"¿Cuántos productos tenemos por cada categoría?"*. 

Para esto necesitamos agrupar los datos. Haremos una consulta un poco más avanzada usando `withCount()`, una herramienta mágica de Laravel que cuenta cuántos elementos "hijos" tiene una relación sin tener que cargarlos en memoria.

Añadimos esto a nuestro laboratorio:

```php
Route::get('/laboratorio-reporte', function () {
    
    $productosAgotados = Producto::where('stock', 0)->count();
    $valorTotalInventario = Producto::selectRaw('SUM(precio * stock) as total')->value('total');

    // 3. Contar productos por categoría
    // Le pedimos a las categorías que cuenten cuántos productos tienen asociados
    $categoriasConConteo = Categoria::withCount('productos')->get();

    // Preparamos un arreglo limpio solo con el nombre y la cantidad
    $desgloseCategorias = [];
    foreach ($categoriasConConteo as $categoria) {
        $desgloseCategorias[$categoria->nombre] = $categoria->productos_count;
    }

    // Retornamos el informe completo
    return [
        'Resumen General' => [
            'Productos Agotados' => $productosAgotados,
            'Valor Total del Inventario' => $valorTotalInventario,
        ],
        'Desglose por Categoria' => $desgloseCategorias
    ];
});
```

## 🏆 4. El Resultado

Si accedes a tu ruta, deberías ver una estructura de datos perfecta, lista para ser inyectada en un panel de control o en un reporte gerencial:

```json
{
  "Resumen General": {
    "Productos Agotados": 12,
    "Valor Total del Inventario": "45230.50"
  },
  "Desglose por Categoria": {
    "Electrónica": 15,
    "Ropa": 20,
    "Hogar": 15
  }
}
```

---

### 🚀 Siguiente paso
Nuestros cálculos matemáticos son precisos y la base de datos está haciendo todo el trabajo pesado. Sin embargo, no podemos pedirle al jefe que entre a una URL rara para ver un archivo JSON negro. 

En el **Tema 2**, aprenderemos a empaquetar estos mismos datos (`$productosAgotados`, `$valorTotalInventario`, `$desgloseCategorias`) y enviarlos a través de un elegante correo electrónico corporativo usando los **Mailables** y vistas **Markdown**.