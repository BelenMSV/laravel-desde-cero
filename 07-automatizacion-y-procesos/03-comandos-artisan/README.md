# Tema 3: Comandos Artisan Personalizados

Artisan es el asistente de línea de comandos de Laravel. Hasta ahora lo has usado para crear controladores, modelos y migraciones. Pero, ¿sabías que puedes enseñarle trucos nuevos?

En este tema vamos a sacar toda la lógica de nuestra ruta temporal `/laboratorio-reporte` y la vamos a meter dentro de un **Comando Artisan Personalizado**. Esto nos permitirá ejecutar nuestro reporte directamente desde la terminal del servidor, sin necesidad de un navegador web.

## 🛠️ 1. Crear el Comando

Abre tu terminal y dile a Artisan que quieres crear un nuevo comando:

```bash
php artisan make:command EnviarReporteInventario
```

Esto creará un nuevo archivo en la carpeta `app/Console/Commands/EnviarReporteInventario.php`. Ábrelo.

## ✍️ 2. Configurar la Firma y Descripción

La "Firma" (`signature`) es el nombre que escribirás en la terminal para ejecutar tu código. La "Descripción" es lo que aparecerá cuando alguien escriba `php artisan list` para ver la ayuda.

Cambia las propiedades `$signature` y `$description` al principio de tu archivo:

```php
    /**
     * El nombre y firma del comando de consola.
     */
    protected $signature = 'inventario:reporte';

    /**
     * La descripción del comando de consola.
     */
    protected $description = 'Calcula el estado del inventario y envía un correo a los administradores';
```

## 🧠 3. Trasladar la Lógica al método `handle()`

El método `handle()` es el corazón de tu comando. Todo el código que pongas aquí se ejecutará cuando llames a `inventario:reporte`. 

Vamos a traer el código de los temas anteriores, pero con dos novedades: 
1. Usaremos **Fachadas de Consola** (`$this->info()`) para pintar mensajes de colores en la terminal.
2. Usaremos la **Fachada Mail** para *enviar* el correo de verdad, no solo previsualizarlo.

Aquí tienes el código completo para tu clase `EnviarReporteInventario.php`:

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;
use App\Models\Producto;
use App\Models\Categoria;
use App\Mail\ReporteInventarioMail;
use Illuminate\Support\Facades\Mail; // <-- IMPORTANTE: Para enviar correos

class EnviarReporteInventario extends Command
{
    protected $signature = 'inventario:reporte';
    protected $description = 'Calcula el estado del inventario y envía un correo a los administradores';

    public function handle()
    {
        // 1. Mensaje en la terminal (Color Verde)
        $this->info('Iniciando el cálculo del reporte de inventario...');

        // 2. Cálculos (Idéntico al Tema 1)
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

        // 3. Avisamos que vamos a enviar el correo
        $this->line('Cálculos terminados. Enviando correo...');

        // 4. ¡ENVIAMOS EL CORREO!
        // En una app real, podrías traer estos correos de la base de datos de usuarios
        $destinatarios = ['admin@mitienda.com', 'gerente@mitienda.com'];
        
        Mail::to($destinatarios)->send(new ReporteInventarioMail($datos));

        // 5. Mensaje de éxito final en la terminal
        $this->info('¡Reporte enviado con éxito a ' . count($destinatarios) . ' destinatarios!');
    }
}
```

## 🚀 4. ¡Ejecutar el Comando!

Antes de ejecutarlo, asegúrate de tener configurado tu archivo `.env` para enviar correos. Si estás en un entorno local, te recomiendo usar [Mailtrap](https://mailtrap.io/) o configurar `MAIL_MAILER=log` para que los correos se guarden como texto en `storage/logs/laravel.log`.

Abre tu terminal y escribe la magia:

```bash
php artisan inventario:reporte
```

Si todo ha ido bien, verás en tu terminal cómo aparecen los textos:
* 🟢 *Iniciando el cálculo del reporte de inventario...*
* ⚪ *Cálculos terminados. Enviando correo...*
* 🟢 *¡Reporte enviado con éxito a 2 destinatarios!*

*(Nota de limpieza: Ya puedes ir a `routes/web.php` y borrar la ruta temporal `/laboratorio-reporte`. ¡Ya no la necesitamos!)*

---

### 🚀 Siguiente paso
¡Espectacular! Ahora tu servidor tiene un comando dedicado para hacer este trabajo. Pero todavía dependemos de que un humano abra la terminal y escriba `php artisan inventario:reporte`. 

En el **Tema 4**, aprenderemos a conectar este comando al **Planificador de Tareas (Task Scheduler)** de Laravel, convirtiendo nuestro código en un robot desatendido que trabajará mientras nosotros dormimos.