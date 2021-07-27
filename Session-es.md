# Session


Ejemplo:

```bash
<?php

namespace ...;

use App\Session\Session;

```

### GET 
Ejemplo sobre sesión:
```bash
$_SESSION
├── bands
│   ├── pantera
│   └── metallica
│   └── megadeth
└── other...
```
Para obtener todas las claves de `bands`:
```bash
session()->get('bands');
```
Para obtener una clave ordinal:
```bash
session()->get('bands.metal')[1];
```
salida:
```bash
metallica
```

## PUT
// example: $_SESSION['bands']['metal'] = 'pantera';
echo '<pre>';
// print_r( session()->put('bands.metal', 'metallica') );                   // update with string value
// print_r( session()->put('bands.metal', ['metallica', 'pantera']) );      // update with array value
// print_r( session()->put('bands.pop', 'michael jackson') );               // create new string/array value on parent key
// print_r( session()->put('bands.pop.classic', 'michael jackson') );       // exception error on 2rd key "pop"
echo '</pre>';

// PUSH session()->push('{existing.arrayPath}', '{array}'); //
echo 'PUSH';
// example: $_SESSION['bands']['metal'] = 'pantera';
echo '<pre>';
// print_r( session()->push('bands.metal', 'megadeth') );               // update update key with [1] => metallica
// print_r( session()->push('bands', 'pop') );                          // update key with [0] => pop (key one level depth sencond mehod attribute is a must)
// print_r( session()->push('bands.pop') );                             // update key with [pop] => ''
// print_r( session()->push('bands.pop', 'michael jackson') );          // update key with [pop] => '' (second attribute is overlap with empty value)
// print_r( session()->push('bands.pop.classic', 'michael jackson') );  // exception error on 2rd key "pop"
echo '</pre>';

// FLUSH session()->flush('{existing.arrayPath}'); //
echo 'FLUSH';
echo '<pre>';
// print_r( session()->flush('bands.metal') ); // update
echo '</pre>';

// FORGET session()->forget('{existing.arrayPath}'); //
echo 'FORGET';
echo '<pre>';
// print_r( session()->forget('bands') ); // update
// print_r( session()->forget('metal') ); // update
echo '</pre>';


