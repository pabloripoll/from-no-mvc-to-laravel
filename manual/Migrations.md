# Migraciones

Es recomendable que el mantenimiento de la base de datos sea administrado por el paquete Doctrine y sea ejecutado por alguno de los frameworks Laravel ó Symfony.
Luego de varios exámenes de ambos frameworks, se considera a Symfony con exacelente prestación para la manipulación de las altas y bajas de migraciones y es de menor peso en bytes, ya que requiere menores cantidades de dependencias de otros paquetes de Composer.
Sin embargo, con respecto con la experiencia de usuario para la administración y mantenimiento, Laravel porta una mejor interfaz por lo que la recomendación de la elección de Laravel sobre Symfony, es enfocado en la practicidad.

La aplicación para el mantenimiento de la base de datos estará desacoplada de la aplicación principal ya que la ejecución de los comandos para la migración de las diferentes versionas de migraciones, será exclusivamente a través de la entreda por terminal en el servidor.

Como la base de datos ya está existe y a su vez contiene datos de uso por el cliente, solo bastará que cada commit de una rama en particular, dentro del servidor del cliente se ejecute dentro del directorio donde reside Laravel, el comando `php artisan migrate`. Este punto se desarrollará luego de la description de mantenimiento *workflow*

## Estructura
La estructura donde estará montada ésta aplicación será la siguiente:
```bash
root
├── app
|   └── mvc
│       ├── app
│       └── bootstrap
│       └── ...
└── *laravel*
```

## Workflow

### # Documentación

Antes que nada, los siguientes enlaces serán la documentación a consultar para el mantenimiento.

[Documentación de los esquemas para las migraciones en Laravel](https://laravel.com/docs/5.0/schema)

### # Crear nueva tabla

Se debe cambiar a directorio `laravel` para ejecutar los comandos a través de `php artisan`.
```bash
$ cd /var/www/{cloud-gestion-dir-path}/laravel/
```
A continuación se tomará de ejemplo la rama Entrega de Material que necesitará una nueva tabla en la base de datos: `entrega_material`
```bash
$ php artisan make:migration create_entrega_material_table
```
Salida:
```bash
Created Migration: 2021_07_29_135432_create_entrega_material_table
```
Fichero php a modificar ubicado en `/laravel/database/migrations` será en se establece el esquema de la tabla.
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
Una vez establecido las columnas, índices y claves foráneas de la table, se procede a crear la tabla en la base de datos con el siguiente comando
```bash
$ php artisan migrate
```
En caso de retroceder en ésta última migración, se deberá realizar a través de un rollback
```bash
$ php artisan migrate:rollback
```
Si se ha producido un error al ejecutar la migración, el comando migrate:rollaback no eliminará la tabla si ya fué creada puesto que puede ocurrir que un error se haya producido al crear una columna y como la grabación de la migración sucede al finalizar cada creación de tabla completa, se deberá proceder eliminar (DROP) la tabla manualmente.

### # Actualizar tabla existente
Para actualizar una tabla existente en la base de datos

### # Modificar nombre de una tabla existente
Para actualizar una tabla existente en la base de datos

### # Eliminar tabla existente
Para actualizar una tabla existente en la base de datos

## Instalación de Laravel 8.x
Laravel se instalará parcialmente en el cliente cuando sea hecho el primer GIT Commit, pero para que esté instalado por completo habrá que cambiar al directio `laravel` y ejecutar los siguientes comandos.
```bash
$ composer install
```
El grupo de Apache suele ser `www-data` pero en caso de desconocer o para asegurarse de saber el nombre del grupo, puede ser ejecutado el siguiente comando
```bash
$ ps aux | egrep '(apache|httpd)'
```
Ahora, establecer los permision y propiedad de todo lo contenido en el directorio. `$USER` es el actual usuario conectado al terminal del servidor pero en caso contrario que el propietario del directorio de la aplicación sea otro, usar el nombre correspondiente.
```bash
$ sudo find . -type f -exec chmod 644 {} \;
$ sudo find . -type d -exec chmod 755 {} \;
$ sudo chown -R $USER:www-data .
$ sudo chgrp -R www-data storage bootstrap/cache
$ sudo chmod -R ug+rwx storage bootstrap/cache
```
Seguido, se deberá establecer la clave que Laravel requiere para la aplicación y vaciar la misma (puesta a cero) de cualquier fichero no deseado en la instalación 
```bash
$ php artisan key:generate
$ php artisan cache:clear
$ php artisan config:clear
```
En cuanto a Composer, se requiere que se actualice el índice de las clases
```bash
$ composer dump-autoload
```
Por último, es fundamental establecer los datos de la conexión a la base de datos del cliente
```bash
$ sudo nano .env 
```
.env file with minimum params required
```bash
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:bFQsdwjwgdqiD9s9LEZ8MWa5CEvNG1GndzAc8HrDcF8=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE={database_name}
DB_USERNAME={database_username}
DB_PASSWORD={database_password}

BROADCAST_DRIVER=log
CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=sync
```
#### Cabe destacar que toda esta configuración será ejecutada solo la primera vez. Luego, como menciona al principio, en el workflow diario, se ejecutará solamente los comandos correspondientes a la actualización de la base de datos:
```bash
/laravel $ php artisan migrate
```
