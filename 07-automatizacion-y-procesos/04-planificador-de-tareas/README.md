# Tema 4: El Planificador de Tareas (Task Scheduler)

En el pasado, si querías que un servidor ejecutara 10 tareas automáticas diferentes, tenías que entrar al sistema operativo (Linux) y configurar 10 "Cron Jobs" horribles y difíciles de leer.

Laravel revoluciona esto. Solo necesitas configurar **un único Cron Job en tu servidor** que se ejecuta cada minuto, y Laravel se encarga de evaluar, usando sintaxis PHP limpia y legible, qué tareas deben ejecutarse en ese momento.

## ⏱️ 1. Programar nuestro Comando

En Laravel 11, la planificación de tareas se configura en un archivo muy específico. Abre `routes/console.php`.

Verás que ya viene con algo de código de ejemplo (como el comando `inspire`). Vamos a importar la clase `Schedule` y a añadir nuestro reporte de inventario al final del archivo.

```php
<?php

use Illuminate\Foundation\Inspiring;
use Illuminate\Support\Facades\Artisan;
use Illuminate\Support\Facades\Schedule; // <-- IMPORTANTE: Importar Schedule

Artisan::command('inspire', function () {
    $this->comment(Inspiring::quote());
})->purpose('Display an inspiring quote')->hourly();

// ----------------------------------------------------
// NUESTRO PLANIFICADOR DE TAREAS
// ----------------------------------------------------

// Le decimos a Laravel: "Ejecuta el reporte de inventario semanalmente, el día 1 (Lunes), a las 08:00 AM".
Schedule::command('inventario:reporte')->weeklyOn(1, '08:00');
```

## 📅 2. Explorando las Frecuencias

La interfaz fluida de Laravel es casi como leer en inglés. Puedes programar tareas con una precisión increíble. Aquí tienes otros ejemplos útiles que podrías usar en una tienda real:

```php
// Borrar carritos de compra abandonados todos los días a medianoche
Schedule::command('carritos:limpiar')->daily();

// Publicar productos programados cada 5 minutos
Schedule::command('productos:publicar')->everyFiveMinutes();

// Enviar un resumen de ventas el último día del mes a las 23:59
Schedule::command('ventas:resumen-mensual')->lastDayOfMonth('23:59');
```

## 🧪 3. Probando el Planificador en Local

Si configuras algo para "el próximo lunes a las 8 AM", ¿cómo sabes si funciona hoy que es jueves? 

Laravel tiene un comando especial para entornos de desarrollo. Abre tu terminal y ejecuta:

```bash
php artisan schedule:work
```

Este comando simulará el reloj del servidor. Se quedará ejecutándose en tu terminal y revisará cada minuto si hay alguna tarea pendiente. 

*(Truco: Si quieres ver la magia ahora mismo, cambia temporalmente tu código a `Schedule::command('inventario:reporte')->everyMinute();`, espera un minuto y verás cómo tu terminal lanza el comando y envía el correo automáticamente).*

## ⚙️ 4. Configuración en un Servidor Real (Producción)

Cuando subas tu proyecto a un servidor de verdad (como AWS, DigitalOcean o un VPS), no puedes dejar la terminal abierta con `schedule:work`. 

En su lugar, debes decirle al sistema operativo Linux que ejecute el evaluador de Laravel cada minuto. Para ello, entrarías a tu servidor y añadirías esta única línea al archivo de configuración Cron (`crontab -e`):

```bash
* * * * * cd /ruta-a-tu-proyecto-laravel && php artisan schedule:run >> /dev/null 2>&1
```

Esta línea mágica despierta a Laravel cada 60 segundos. Laravel revisa el archivo `routes/console.php`, comprueba la hora, y si coincide con lo que has programado (como tus lunes a las 8:00 AM), ejecuta el comando de forma transparente en segundo plano.

---

### 🚀 Siguiente paso
¡Felicidades! Acabas de automatizar por completo un proceso de negocio. Tu aplicación ya trabaja por sí sola. 

Sin embargo, tenemos un pequeño problema de rendimiento oculto. ¿Qué pasa si en el futuro tenemos que enviar este reporte a 100 gerentes distintos? El servidor se congelaría intentando procesar tantos correos a la vez. En el **Tema 5**, solucionaremos esto enviando las tareas pesadas a las **Colas (Queues y Jobs)**.