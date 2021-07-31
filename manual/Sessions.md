# Session

Ejemplo de uso de `$_SESSION` en controlador
```bash
<?php

namespace App\Http\Controllers;

use App\Session\Session;

class ExampleController
{    
    public function ListPage()
    {
        $session = new Session;
        
        $user_id = ($session()->has('user.id') ? $session()->get('user.id') : false;
    
        return $user_id;
    }
}
```
Ejemplo de uso de `$_SESSION` a través de `Global Functions` como `session()`
```php
<?php

$user_id = (session()->has('user.id') ? session()->get('user.id') : false);

echo $user_id;

```
En caso de ver conveniente, se puede agregar session() como método con el mismo nombre a la clase Request para obtener los valores como una propiedad del mismo *(simil Laravel)*
```php
<?php

echo $request->session()->get('user.id');
```

### Método ALL 
Para obtener todas las variables de sesión como el siguiente ejemplo
```php
<?php

$session = $_SESSION;
```
sólo bastará con llamar a la función session() para obtener las claves
```php
<?php

$session = session()->all();

echo $session['user']['id'];
```
Como también se podría llamar a una clave en la misma línea:
```php
<?php

echo session()->all()['user']['id'];
```
Pero para llamar a claves dentro de la sesión es conveniente para una buena práctica, obtener los valores de la clave con el método GET.

### Método GET 
Ejemplo de sesión pre-establecida:
```bash
$_SESSION
├── [bands]
|   └── [metal]
│       ├── [0] pantera
│       └── [1] metallica
│       └── [2] megadeth
└── [other]
```
Para obtener todas las claves de `bands`:
```php
<?php

$bands = session()->get('bands');
```
Salida:
```bash
metal => Array
```
Para obtener una clave ordinal:
```php
<?php

$metalArtists = session()->get('bands.metal');

echo $metalArtists[1];
```
Salida:
```bash
metallica
```
También se puede obtener una clave ordinal de la siguiente manera
```php
<?php

$metalArtists = session()->get('bands.metal')[1];

echo $metalArtists;
```
Salida:
```bash
metallica
```

## Método PUT
Establecer una nueva clave en sesión. Nótese que cuando se inicializa primera clave puede prescindir de pasar el segundo atributo al método
```php
<?php

session()->put('bands');
```
Salida:
```bash
$_SESSION
└── [bands]
```
Actualizar clave con un valor (string) . Nótese que se puede agregar hasta una sub clave no existente en el atributo $keys.
```php
<?php

session()->put('bands.metal', 'metallica');
```
Resultado:
```bash
$_SESSION
└── [bands]
    └── [metal] metallica
```
Pero arrojará error cuando se intente establecer una profundidad de dos claves inexistentes.
```php
<?php

session()->put('bands.metal.classic', 'metallica');
```
Salida:
```bash
Exception error:
```
Actualizar clave con valor array
```php
<?php

$array = ['metallica', 'pantera'];
session()->put('bands.metal', $array);
```
Resultado:
```bash
$_SESSION
└── [bands]
    └── [metal]
        ├── [0] metallica
        └── [1] pantera
```
Agregar nueva clave con valor dentro de clave existente
```php
$popArtists = ['peter gabriel', 'phil collins'];
session()->put('bands.pop', $popArtists);
```
Resultado:
```bash
$_SESSION
└── [bands]
    └── [metal]
    |   ├── [0] metallica
    |   └── [1] pantera
    └── [pop]
        ├── [0] peter gabriel
        └── [1] phil collins
```

## Método PUSH


session()->push('bands.metal', 'megadeth');               // update update key with [1] => metallica
session()->push('bands', 'pop');                          // update key with [0] => pop (key one level depth sencond mehod attribute is a must)
ession()->push('bands.pop');                             // update key with [pop] => ''
session()->push('bands.pop', 'michael jackson');          // update key with [pop] => '' (second attribute is overlap with empty value)
session()->push('bands.pop.classic', 'michael jackson');  // exception error on 2rd key "pop"


## Método FLUSH 

session()->flush('bands.metal');

## Método FORGET

session()->forget('bands');


#### Referencia Laravel

https://laravel.com/docs/8.x/session
