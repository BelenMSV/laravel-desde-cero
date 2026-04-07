# Tema 5: Factories y Seeders (Poblando la Base de Datos)

Probar una aplicación web sin datos es como probar un coche sin gasolina. No puedes ver cómo se comporta el diseño con listas largas, ni probar si la paginación funciona.

En lugar de crear 50 usuarios o "Posts" a mano uno por uno, Laravel nos ofrece **Factories** y **Seeders** para generar miles de registros de prueba (con nombres, textos e imágenes falsas) en cuestión de segundos.



## 🏭 1. ¿Qué es un Factory? (El Molde)

Un Factory es como el "molde" de una fábrica. Le enseña a Laravel cómo debe fabricar un registro falso para un Modelo específico utilizando una librería maravillosa llamada **Faker**.

Vamos a crear un Factory para nuestro modelo `Post`:

```bash
php artisan make:factory PostFactory
```

Abre el archivo generado en `database/factories/PostFactory.php` y define cómo será tu Post falso en el método `definition()`:

```php
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    public function definition(): array
    {
        return [
            // fake() genera datos aleatorios súper realistas
            'titulo' => fake()->sentence(), // Genera una frase corta
            'contenido' => fake()->paragraphs(3, true), // Genera 3 párrafos de texto
            'esta_publicado' => fake()->boolean(), // Genera true o false aleatoriamente
        ];
    }
}
```

## 🌱 2. ¿Qué es un Seeder? (El Sembrador)

Si el Factory es el molde, el **Seeder** es el obrero que presiona el botón de "Fabricar" y guarda los datos en la base de datos.

Abre el archivo `database/seeders/DatabaseSeeder.php`. Este es el sembrador principal de tu aplicación. Bórralo todo y déjalo así:

```php
<?php

namespace Database\Seeders;

use App\Models\Post;
use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        // ¡Magia! Le decimos a Laravel que fabrique 50 Posts y los guarde.
        Post::factory(50)->create();
    }
}
```

## 🚀 3. Ejecutando la Siembra

Ahora solo queda decirle a Artisan que ejecute nuestro sembrador. En tu terminal:

```bash
php artisan db:seed
```

¡Boom! 💥 Si revisas tu base de datos (o usas Tinker como vimos en el tema anterior y escribes `Post::count();`), verás que ahora tienes 50 artículos perfectamente redactados y listos para usar en tus vistas.

> **💡 El Combo Destructor:**
> Cuando estás desarrollando, es muy común querer borrar toda la base de datos y volver a crearla desde cero con datos nuevos. Puedes hacer esto en un solo comando:
> `php artisan migrate:fresh --seed`
> *(Atención: ¡Nunca uses `migrate:fresh` en un servidor de producción real o borrarás los datos de tus clientes!)*

---

### 🚀 Siguiente paso
Nuestra base de datos ya está repleta de información. ¡Es el momento de sacarla de ahí y mostrarla en nuestras Vistas! En el siguiente tema, profundizaremos en **Eloquent** para aprender cómo filtrar, buscar y manipular estos datos como todo un experto.