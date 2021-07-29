# Migraciones

## Razón de uso
El mantenimiento de la base de datos será administrado por el paquete Doctrine y es ejecutado por framework Laravel ó Symfony.
Luego de varios exámenes de ambos frameworks, se considera a Symfony con exacelente prestación para la manipulación de las altas y bajas de migraciones y es de menor peso en bytes al recurrir a menores cantidades de dependencias al ser instalado. Sin embargo, al respecto experiencia de usuario, Laravel porta una mejor interfaz ...
Ésta aplicación estará desacoplada de la aplicación principal y la ejecución de los comandos para la migración de las diferentes versionas de la base de datos, será exclusivamente a través de la entreda por terminal en el servidor.

## Estructura

```bash
root
├── app
|   └── mvc
│       ├── app
│       └── bootstrap
│       └── ...
└── laravel
```

## Workflow

### # Crear nueva tabla
Para crear una tabla nueva en la base de datos

https://laravel.com/docs/5.0/schema

Se debe cambiar a directorio `laravel` para ejecutar los comandos a través de `php artisan`. En este ejemplo será la tabla `entrega_material`
```bash
$ php artisan make:migration create_entrega_material_table
```
Salida:
```bash
Created Migration: 2021_07_29_135432_create_entrega_material_table
```
Fichero php a modificar ubicado en `/laravel/database/migrations`
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEntregaMaterialTable extends Migration
{
    /**
     * Table name property.
     *
     * @return string
     */
    private $table = 'entrega_material';

    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create($this->table, function (Blueprint $table) {
            $table->increments('IdEntregaMaterial');
            $table->integer('IdSerie');
            $table->integer('IdAlmacen');
            $table->integer('IdTrabajador');
            $table->integer('Numero');
            $table->date('Fecha');
            $table->boolean('Estado');
            $table->decimal('Total', 10, 2);
            $table->text('Contenido');
            $table->text('Notas');
            $table->string('URLFirma', 256);
            $table->timestamps();

            $table->index('IdSerie');
            $table->index('IdAlmacen');
            $table->index('IdTrabajador');
            $table->index('Numero');
            $table->index('Fecha');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists($this->table);
    }
}
```
Una vez establecido las colmnas e índices de la table, se proce a crear la tabla con el siguiente comando
```bash
$ php artisan migrate
```
En caso de retroceder en ésta última migración, se deberá realizar a través de un rollback
```bash
$ php artisan migrate:rollback
```
Si se ha producido un error al ejecutar la migración, el comando migrate:rollaback no eliminará la tabla y para lograr este objetivo de deberá proceder manualmente.

### # Actualizar tabla existente
Para actualizar una tabla existente en la base de datos

### # Modificar nombre de una tabla existente
Para actualizar una tabla existente en la base de datos

### # Eliminar tabla existente
Para actualizar una tabla existente en la base de datos

## Instalación de Laravel 8.x
La instalación de Symfony en el cliente será a través del GIT Commit.
