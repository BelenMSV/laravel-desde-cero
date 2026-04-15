# Tema 2: Correos Profesionales con Mailables y Markdown

Enviar un correo con texto plano es poco profesional, y crear correos en HTML desde cero es una tarea tediosa debido a la falta de estándares entre los distintos clientes de correo (Gmail, Outlook, etc.). 

Laravel resuelve este problema introduciendo los **Mailables** y las plantillas **Markdown**. Con un par de etiquetas, Laravel generará automáticamente botones, tablas y paneles con un diseño corporativo impecable y adaptable a móviles.

## ✉️ 1. Generar el Mailable

Un "Mailable" es una clase en Laravel que representa un tipo específico de correo (ej. `ReciboDeCompra`, `BienvenidaUsuario`, o en nuestro caso, `ReporteInventarioMail`).

Abre tu terminal y ejecuta el siguiente comando. La bandera `--markdown` le dirá a Laravel que también nos cree la vista asociada:

```bash
php artisan make:mail ReporteInventarioMail --markdown=emails.reporte_inventario
```

## 🧠 2. Configurar la Clase del Correo

Abre el archivo recién creado en `app/Mail/ReporteInventarioMail.php`. 

Aquí definiremos tres cosas: 
1. Los **datos** que recibirá el correo (nuestras estadísticas).
2. El **asunto** del correo.
3. La **vista** que utilizará.

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;

class ReporteInventarioMail extends Mailable
{
    use Queueable, SerializesModels;

    // 1. Declaramos una variable pública.
    // Al ser pública, estará disponible automáticamente en nuestra vista Blade.
    public $estadisticas;

    /**
     * El Constructor: Aquí es donde inyectaremos los datos desde nuestro controlador o comando.
     */
    public function __construct($datos)
    {
        $this->estadisticas = $datos;
    }

    /**
     * El Sobre: Configura el Asunto y el Remitente.
     */
    public function envelope(): Envelope
    {
        return new Envelope(
            subject: '📊 Reporte Semanal de Inventario',
        );
    }

    /**
     * El Contenido: Apunta a nuestra vista Markdown.
     */
    public function content(): Content
    {
        return new Content(
            markdown: 'emails.reporte_inventario',
        );
    }

    public function attachments(): array
    {
        return [];
    }
}
```

## 🎨 3. Diseñar la Plantilla con Componentes Markdown

Ahora vamos a darle estilo a nuestro correo. Abre el archivo `resources/views/emails/reporte_inventario.blade.php`.

Borra lo que hay dentro y usa los componentes `<x-mail::message>`, `<x-mail::panel>` y `<x-mail::table>` para crear un Dashboard ejecutivo. Fíjate cómo usamos la variable `$estadisticas` que definimos en la clase.

```html
<x-mail::message>
# Resumen Semanal de Inventario

Hola equipo,

Aquí tenéis el reporte automático del estado actual de nuestro catálogo de productos:

<x-mail::panel>
**Productos Agotados (Stock 0):** {{ $estadisticas['Agotados'] }} ud.
<br>
**Valor Total del Inventario:** ${{ $estadisticas['Valor Total ($)'] }}
</x-mail::panel>

### Desglose por Categoría

<x-mail::table>
| Categoría       | Total de Productos |
| :------------- | :------------------: |
@foreach ($estadisticas['Desglose por Categoria'] as $categoria => $cantidad)
| **{{ $categoria }}** | {{ $cantidad }} |
@endforeach
</x-mail::table>

<x-mail::button :url="route('productos.index')">
Ir al Panel de Administración
</x-mail::button>

Saludos automáticos,<br>
El Robot de {{ config('app.name') }}
</x-mail::message>
```

## 👀 4. Previsualizar el Correo (El truco secreto)

Enviar correos reales todo el tiempo para ver si el diseño quedó bien es agotador. Laravel tiene un truco maravilloso: **si devuelves un Mailable en una ruta web, Laravel lo renderiza en el navegador como si fuera una página normal**.

Vamos a actualizar nuestro laboratorio en `routes/web.php` para inyectar nuestros cálculos reales en el correo y verlo en pantalla:

```php
use App\Models\Producto;
use App\Models\Categoria;
use App\Mail\ReporteInventarioMail; // <-- Importamos nuestro nuevo Mailable

Route::get('/laboratorio-reporte', function () {
    
    // 1. Calculamos los datos (Lo que hicimos en el Tema 1)
    $productosAgotados = Producto::where('stock', 0)->count();
    $valorTotalInventario = Producto::selectRaw('SUM(precio * stock) as total')->value('total');

    $categoriasConConteo = Categoria::withCount('productos')->get();
    $desgloseCategorias = [];
    foreach ($categoriasConConteo as $categoria) {
        $desgloseCategorias[$categoria->nombre] = $categoria->productos_count;
    }

    $datos = [
        'Agotados' => $productosAgotados,
        'Valor Total ($)' => number_format($valorTotalInventario, 2),
        'Desglose por Categoria' => $desgloseCategorias
    ];

    // 2. ¡Magia! En lugar de devolver un JSON, devolvemos el Mailable
    return new ReporteInventarioMail($datos);
});
```

Ve a tu navegador y entra a `http://localhost:8000/laboratorio-reporte`. 
¡Deberías ver un correo bellamente formateado, con tu logotipo (si tienes uno configurado), botones funcionales y una tabla de datos dinámica!

---

### 🚀 Siguiente paso
Nuestros datos son correctos y el diseño del correo es impecable. Sin embargo, seguimos teniendo que visitar una URL manualmente para ver esto. En el **Tema 3**, aprenderemos a trasladar toda esta lógica a un **Comando Artisan Personalizado** para que podamos generar y enviar este reporte directamente desde la terminal del servidor.