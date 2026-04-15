# Tema 6: Generación de PDFs y Archivos Adjuntos

En el entorno corporativo, los correos electrónicos son el medio de transporte, pero el documento real suele ser un archivo adjunto (como una factura o un reporte mensual).

Laravel no trae generación de PDF por defecto, pero su ecosistema de paquetes es inmenso. Vamos a usar la librería más popular para esto: **Laravel DomPDF**. Esta herramienta coge cualquier vista HTML/Blade y la "imprime" en un archivo PDF.

## 📦 1. Instalación del Paquete

Abre tu terminal y dile a Composer (el gestor de paquetes de PHP) que descargue la librería en tu proyecto:

```bash
composer require barryvdh/laravel-dompdf
```
*Laravel 11 detectará e instalará el paquete automáticamente gracias a su sistema de auto-descubrimiento.*

## 📄 2. Crear la Vista del PDF

El HTML para un PDF es un poco diferente al de una web moderna. DomPDF no entiende del todo utilidades complejas de Tailwind CSS como `grid` o `flexbox`. Lo mejor para los PDFs es usar un diseño limpio con tablas clásicas y CSS básico.

Crea un nuevo archivo en `resources/views/emails/reporte_pdf.blade.php`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Reporte de Inventario</title>
    <style>
        body { font-family: sans-serif; color: #333; }
        .header { text-align: center; border-bottom: 2px solid #2563eb; padding-bottom: 10px; margin-bottom: 20px; }
        .resumen { background-color: #f3f4f6; padding: 15px; border-radius: 5px; margin-bottom: 20px; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { border: 1px solid #ddd; padding: 10px; text-align: left; }
        th { background-color: #2563eb; color: white; }
    </style>
</head>
<body>

    <div class="header">
        <h1>📊 Reporte de Inventario</h1>
        <p>Generado automáticamente el: {{ now()->format('d/m/Y H:i') }}</p>
    </div>

    <div class="resumen">
        <h3>Resumen General</h3>
        <p><strong>Productos Agotados:</strong> {{ $estadisticas['Agotados'] }} ud.</p>
        <p><strong>Valor del Inventario:</strong> ${{ $estadisticas['Valor Total ($)'] }}</p>
    </div>

    <h3>Desglose por Categorías</h3>
    <table>
        <thead>
            <tr>
                <th>Categoría</th>
                <th>Total de Productos</th>
            </tr>
        </thead>
        <tbody>
            @foreach ($estadisticas['Desglose por Categoria'] as $categoria => $cantidad)
            <tr>
                <td>{{ $categoria }}</td>
                <td>{{ $cantidad }}</td>
            </tr>
            @endforeach
        </tbody>
    </table>

</body>
</html>
```

## 📎 3. Generar y Adjuntar el PDF en el Mailable

Ahora tenemos que decirle a nuestro correo electrónico que, justo antes de enviarse, genere este PDF y lo adjunte. 

Abre la clase de tu correo: `app/Mail/ReporteInventarioMail.php`. Vamos a importar dos clases importantes arriba del todo y a modificar el método `attachments()`.

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;
// 1. IMPORTACIONES NUEVAS
use Illuminate\Mail\Mailables\Attachment; 
use Barryvdh\DomPDF\Facade\Pdf; 

class ReporteInventarioMail extends Mailable
{
    use Queueable, SerializesModels;

    public $estadisticas;

    public function __construct($datos)
    {
        $this->estadisticas = $datos;
    }

    public function envelope(): Envelope
    {
        return new Envelope(
            subject: '📊 Reporte Semanal de Inventario',
        );
    }

    public function content(): Content
    {
        return new Content(
            markdown: 'emails.reporte_inventario',
        );
    }

    /**
     * El método de Archivos Adjuntos
     */
    public function attachments(): array
    {
        // 2. Generamos el PDF al vuelo usando nuestra vista y pasándole los datos
        $pdf = Pdf::loadView('emails.reporte_pdf', ['estadisticas' => $this->estadisticas]);

        // 3. Devolvemos el PDF como un archivo adjunto
        return [
            Attachment::fromData(fn () => $pdf->output(), 'Reporte_Inventario_Semanal.pdf')
                ->withMime('application/pdf'),
        ];
    }
}
```

## 🚀 4. ¡La Prueba Final!

¡No tienes que cambiar nada más! Tu Trabajo (`Job`), tu Comando Artisan y tu Planificador (`Schedule`) seguirán funcionando exactamente igual, pero ahora la clase Mailable se encargará de crear el PDF mágicamente.

Para probarlo, asegúrate de tener tu trabajador de colas encendido (`php artisan queue:work`) y en otra terminal ejecuta tu comando maestro:

```bash
php artisan inventario:reporte
```

Si revisas tu sistema de correos local (o Mailtrap/Mailhog), verás que el correo llega con su bonito diseño de Markdown en el cuerpo del mensaje, ¡**y con un documento PDF profesional adjunto listo para descargar**!

---

### 🎉 ¡MÓDULO 7 COMPLETADO! Y CON ELLO... ¡EL CURSO!

Has pasado de no saber qué era un "Controlador" a construir un sistema empresarial completo, con interfaz gráfica, protección de datos, subida de archivos, operaciones en segundo plano, comandos personalizados y reportes automáticos en PDF. 

Eres oficialmente capaz de desarrollar aplicaciones backend robustas y modernas con Laravel. ¡El límite ahora es tu imaginación!