# Tema 5: Colas y Trabajos en Segundo Plano (Queues & Jobs)

Uno de los mayores errores de rendimiento en aplicaciones web es realizar tareas "pesadas" (como enviar correos, procesar imágenes o generar reportes) de forma síncrona. Si el usuario hace clic en "Comprar", no debería tener que esperar 5 segundos mirando una pantalla de carga solo porque el servidor está intentando enviarle el recibo por correo.

Laravel soluciona esto con el sistema de **Colas**. En este tema, sacaremos el envío de correos de nuestro comando Artisan y lo meteremos en un *Job* (Trabajo en segundo plano).

## ⚙️ 1. Configurar la Conexión de la Cola

Por defecto, Laravel ejecuta las tareas de forma "síncrona" (inmediata). Vamos a decirle que guarde las tareas en nuestra base de datos para procesarlas poco a poco.

Abre tu archivo `.env` y busca la variable `QUEUE_CONNECTION`. Cámbiala de `sync` (o `database` si ya estaba así en Laravel 11) para asegurarnos:

```env
QUEUE_CONNECTION=database
```

*Nota: En Laravel 11, la tabla para las colas (`jobs`) ya viene incluida en las migraciones iniciales (`0001_01_01_000002_create_jobs_table.php`). Si por algún motivo no la tienes, puedes generarla con `php artisan queue:table` y luego ejecutar `php artisan migrate`.*

## 👷 2. Crear el Trabajo (Job)

Vamos a crear una clase específica cuyo único propósito en la vida será enviar nuestro reporte. Abre tu terminal:

```bash
php artisan make:job EnviarReporteInventarioJob
```

Esto crea el archivo `app/Jobs/EnviarReporteInventarioJob.php`. Ábrelo y vamos a configurarlo. Fíjate que el método `__construct` recibe los datos, y el método `handle` hace el trabajo pesado.

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Mail;
use App\Mail\ReporteInventarioMail;

class EnviarReporteInventarioJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public $datosReporte;
    public $destinatario;

    /**
     * Create a new job instance.
     */
    public function __construct($datos, $email)
    {
        $this->datosReporte = $datos;
        $this->destinatario = $email;
    }

    /**
     * Execute the job.
     */
    public function handle(): void
    {
        // Esta es la tarea pesada que tomará un par de segundos por correo
        Mail::to($this->destinatario)->send(new ReporteInventarioMail($this->datosReporte));
    }
}
```

## 🔄 3. Delegar la tarea desde el Comando

Ahora volvemos a nuestro comando original (`app/Console/Commands/EnviarReporteInventario.php`). 

En lugar de detenerse a enviar los correos uno por uno con la fachada `Mail`, simplemente **despacharemos (dispatch)** el trabajo a la cola. ¡Esto es casi instantáneo!

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;
use App\Models\Producto;
use App\Models\Categoria;
// 1. Importamos nuestro nuevo Job
use App\Jobs\EnviarReporteInventarioJob; 

class EnviarReporteInventario extends Command
{
    protected $signature = 'inventario:reporte';
    protected $description = 'Calcula el estado del inventario y envía un correo a los administradores';

    public function handle()
    {
        $this->info('Calculando reporte...');

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

        $destinatarios = ['admin@mitienda.com', 'gerente@mitienda.com', 'ceo@mitienda.com'];

        $this->line('Despachando correos a la cola de trabajo...');

        // 2. ENVIAMOS LOS TRABAJOS A LA COLA
        foreach ($destinatarios as $email) {
            // El comando dispatch mete el trabajo en la base de datos de forma instantánea
            EnviarReporteInventarioJob::dispatch($datos, $email);
        }

        // El comando terminará en milisegundos, sin importar si son 3 o 300 correos.
        $this->info('¡' . count($destinatarios) . ' trabajos de correo añadidos a la cola!');
    }
}
```

## 🚀 4. Probando la Cola de Trabajos

Si ahora ejecutas en tu terminal `php artisan inventario:reporte`, verás que termina *inmediatamente*. Sin embargo, **los correos no se han enviado**. Si revisas tu base de datos (tabla `jobs`), verás que hay 3 registros esperando allí.

¿Quién hace el trabajo entonces? Necesitamos encender al "Trabajador" (Worker).

Abre **otra pestaña o ventana de tu terminal** (deja tu servidor `php artisan serve` corriendo en la otra) y ejecuta:

```bash
php artisan queue:work
```

¡Magia! Verás en tiempo real cómo el trabajador detecta los trabajos en la base de datos y comienza a procesarlos uno por uno. Tu aplicación principal sigue libre y rápida, mientras este proceso invisible hace el trabajo sucio.

*(Nota: En producción, se utiliza un programa de servidor llamado `Supervisor` para mantener este comando `queue:work` ejecutándose eternamente en segundo plano).*

---

### 🚀 Siguiente paso
Tu aplicación ya es capaz de procesar tareas masivas sin despeinarse. Para poner la cereza final sobre el pastel de este curso, en el **Tema 6** vamos a añadir un requerimiento clásico de las oficinas corporativas: transformar nuestro reporte en un **Documento PDF descargable** que enviaremos como archivo adjunto en nuestro correo.