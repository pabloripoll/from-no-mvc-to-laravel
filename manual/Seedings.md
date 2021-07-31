# Laravel Seed

Los seedings sirven para completar las tablas con datos para el testing o para autocompletar tablas con datos estáticos.

[Documentación de Laravel para configurar los Seeds](https://laravel.com/docs/8.x/seeding)

## Ejemplo
Crear el fichero seed
```
$ php artisan make:seeder EntregaMaterialSeeder
```
Configurar fichero creado
```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Str;

class EntregaMaterialSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $entregas = 1;
        $items = [
            ['Anteojos', 25],
            ['Casco', 15],
            ['Botas', 40],
            ['Chaqueta', 30],
            ['Zapatos', 30],
            ['Cinturón', 20],
            ['Mono', 45],
            ['Guantes', 100]
        ];
        $trabajadores = [
            ['id' => 2, 'nombre' => 'Francisco', 'apellidos' => 'Perez Fernandez'],
            ['id' => 3, 'nombre' => 'Luis', 'apellidos' => 'Pecci Herrero'],
            ['id' => 4, 'nombre' => 'Ricardo', 'apellidos' => 'Pina Fernandez'],
            ['id' => 12, 'nombre' => 'Carlos', 'apellidos' => 'Perez Jara'],
            ['id' => 18, 'nombre' => 'Ruben', 'apellidos' => 'Zapardiel'],
            ['id' => 19, 'nombre' => 'Diego', 'apellidos' => 'Hernandez Herraez']
        ];        

        for ($i = 1; $i <= $entregas; $i++) :

            shuffle($trabajadores);
            $trabajador = $trabajadores[0];

            $id = DB::table('entrega_material')->insertGetId([
                'IdSerie'       => 1,
                'IdAlmacen'     => 1,
                'IdTrabajador'  => $trabajador['id'],
                'Fecha'         => date('Y-m-d'),
                'Estado'        => 0,
            ]);

            shuffle($items);
            $total = 0;
            for ($i = 1; $i <= 3; $i++) :

                $concepto   = $items[$i][0];
                $precio     = $items[$i][1];
                $cantidad   = 1;

                DB::table('entrega_material_detalles')->insert([
                    'IdEntregaMaterial' => $id,
                    'Concepto'          => $concepto,
                    'Cantidad'          => $cantidad,
                    'Precio'            => $precio
                ]);

                $total = $total + ($precio * $cantidad);

            endfor;

            DB::table('entrega_material')
                ->where('IdEntregaMaterial', $id)
                ->update(['Total' => $total]);

        endfor;
    }
}
```
Ejecutar el seed *(comando recomendado)*
```bash
$ php artisan db:seed --class=UserSeeder
```
