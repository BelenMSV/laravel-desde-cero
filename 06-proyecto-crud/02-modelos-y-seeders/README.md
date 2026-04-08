# Tema 2: Modelos, Relaciones y Seeders

Nuestras tablas ya existen en la base de datos, pero PHP no sabe cómo comunicarse con ellas. Para eso necesitamos crear los **Modelos**. Además, usaremos la magia de los Factories para llenar nuestra tienda con decenas de productos falsos en cuestión de segundos, lo que nos permitirá diseñar la interfaz visual mucho más fácilmente.

## 🧱 1. Creando los Modelos

En tu terminal, ejecuta los siguientes comandos para crear los modelos de nuestras dos tablas, y aprovecharemos para pedirle a Laravel que también nos cree sus **Factories** (fábricas de datos falsos):

```bash
php artisan make:model Categoria -f
php artisan make:model Producto -f
```

## 🤝 2. Configurando Relaciones y Asignación Masiva

Abre tus nuevos modelos en la carpeta `app/Models/`. Vamos a proteger los campos que se pueden llenar masivamente (`$fillable`) y a crear los "puentes" (relaciones) entre ellos.

**Archivo: `app/Models/Categoria.php`**
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Categoria extends Model
{
    use HasFactory;

    protected $fillable = ['nombre', 'descripcion'];

    // Relación: Una Categoría tiene MUCHOS Productos (1 a N)
    public function productos()
    {
        return $this->hasMany(Producto::class);
    }
}
```

**Archivo: `app/Models/Producto.php`**
```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes; // Lo usaremos en el Tema 8

class Producto extends Model
{
    use HasFactory;

    protected $fillable = [
        'categoria_id', 'nombre', 'descripcion', 
        'precio', 'stock', 'imagen_url'
    ];

    // Relación Inversa: Un Producto PERTENECE a UNA Categoría
    public function categoria()
    {
        return $this->belongsTo(Categoria::class);
    }
}
```

## 🏭 3. Las Fábricas de Datos (Factories)

Los Factories usan una librería llamada `Faker` que genera nombres, textos y números aleatorios que parecen reales.

Abre **`database/factories/CategoriaFactory.php`** y añade esto dentro del método `definition()`:
```php
public function definition(): array
{
    return [
        'nombre' => fake()->unique()->word(), // Una palabra única
        'descripcion' => fake()->sentence(), // Una oración aleatoria
    ];
}
```

Ahora abre **`database/factories/ProductoFactory.php`** y configura sus datos:
```php
public function definition(): array
{
    return [
        // Genera un nombre de 2 a 3 palabras
        'nombre' => fake()->words(3, true), 
        // Genera un párrafo de descripción
        'descripcion' => fake()->paragraph(), 
        // Un precio aleatorio entre 10.00 y 500.00
        'precio' => fake()->randomFloat(2, 10, 500), 
        // Un stock aleatorio entre 0 y 100
        'stock' => fake()->numberBetween(0, 100), 
        // Por ahora dejamos la imagen vacía
        'imagen_url' => null, 
    ];
}
```

## 🌱 4. Sembrando la Base de Datos (Seeders)

Ya tenemos las fábricas listas, pero alguien tiene que pulsar el botón de encendido. Ese es el trabajo del **Seeder**.

Abre el archivo **`database/seeders/DatabaseSeeder.php`**. Borra lo que haya dentro del método `run()` y escribe esto:

```php
namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Categoria;
use App\Models\Producto;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        // Magia pura: Le decimos a Laravel que cree 5 Categorías,
        // y que por cada categoría, fabrique automáticamente 10 Productos.
        Categoria::factory(5)
            ->has(Producto::factory()->count(10))
            ->create();
            
        // Al final tendremos 5 categorías y 50 productos en total.
    }
}
```

## 🪄 5. Ejecutando la Magia

Abre tu terminal y ejecuta este comando, que borrará cualquier dato viejo (fresh) y ejecutará nuestro Seeder (--seed):

```bash
php artisan migrate:fresh --seed
```

¡Boom! 💥 En menos de un segundo, tu base de datos acaba de llenarse con 50 productos perfectamente estructurados, relacionados con sus respectivas categorías y listos para ser mostrados en tu web.

---

### 🚀 Siguiente paso
Con la base de datos llena, ¡nuestra aplicación ya tiene vida! El siguiente paso es crear el "cerebro" y los caminos para que los usuarios puedan ver estos productos. En el próximo tema crearemos las **Rutas y el Controlador de Recursos**.