# Routes

Como imitación de los web routes de Laravel, se ha creado una serie de clases correspondientes para configurar las rutas que se visitarán en la aplicación lo más similar posible a como se realiza en el mencionado framework.

## Re Direccionamiento
primero que todo hay que configurar el fichero .htaccess para redirigir las nuevas rutas o la actualización de las ya existentes al directorio de refactoring de la aplicación
```bash
$ nano .htaccess
```
El principal redireccionamiento será establecer las rutas con los principales siguientes parámetros
```bash
##
#   Refactoring MVC 06/2021
##

RewriteRule ^mvc/(inicio|agenda|ventas|compras|sat|personal|almacen|informes|administracion)/(.*)$ mvc/public/index.php [L]
```

## Rutas
Para actualizar o modificar las rutas se deberá configurar el fichero `app/routes/web.php` que se encuentra en el directorio de refactoring. En el siguiente caso se utilizará la configuración del módulo `Entregas de Material` para los dos ejemplos de uso para establecer la vista.
Para ambos casos se puede configurar la mismas ruta para diferentes tipos de llamadas.

### # Configuración de la vista de forma directa
```php
<?php

use App\Http\Request\Route;
use App\Http\Controllers\PersonalController as Personal;

// Entregas de Material
Route::get('/personal/entrega_material/example', function () {    
    $data = ['name' => 'John', 'surname' => 'Connor', 'age' => 24];
    return view('personal.entrega_material.example', $data); // self is in reference to Kernel class
});

Route::get('/personal/entrega_material/example', function () {    
    $data = session()->get('usuario');
    return view('personal.entrega_material.example', $data);
});

Route::get('/personal/entrega_material/example', function () {
    $data = Personal::EntregaDeMaterial();
    return view('personal.entrega_material.example', $data);
});
```
### # Configuración de la vista a través del controlador *(recomendado)*
Este es un ejemplo con todas las variantes con la que se puede configurar una URN según la llamada que haya sido realizada
```php
<?php

use App\Http\Request\Route;
use App\Http\Controllers\PersonalController as Personal;

// Entregas de Material
Route::get('/personal/entrega_material/listado', [Personal::class, 'listEntregasDeMaterial']);
Route::post('/personal/entrega_material/listado', [Personal::class, 'listEntregasDeMaterial']);
Route::get('/personal/entrega_material/formulario', [Personal::class, 'formEntregaDeMaterial']);
Route::put('/personal/entrega_material/formulario', [Personal::class, 'putEntregaDeMaterial']);
Route::post('/personal/entrega_material/formulario', [Personal::class, 'postEntregaDeMaterial']);
Route::patch('/personal/entrega_material/formulario', [Personal::class, 'patchEntregaDeMaterial']);
Route::delete('/personal/entrega_material/formulario', [Personal::class, 'deleteEntregaDeMaterial']);
```



