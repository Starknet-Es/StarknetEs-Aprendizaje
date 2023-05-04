<div align="center">
  <h1 style="font-size: larger;">
    <strong>Explorando Cairo 1.0: Un lenguaje de alto nivel similar a Rust para programas demostrables (4)</strong> 
    </h1>  
    <img src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/Explorando_Cairo1_pt4.png" width="600">

_[Artículo Original](https://medium.com/dev-genius/exploring-cairo-1-0-a-rust-like-high-level-language-for-provable-programs-4-53ae2070888a)_ - Realizado por el compañero [Asten](https://twitter.com/0xasten)
</div>

## Introducción

Una de las estructuras de datos clave en la programación es un array (matriz en español), y Cairo 1.0 proporciona una implementación integrada de arrays como tipo de datos. En esta publicación, discutiremos cómo crear un array en Cairo 1.0 y cómo agregar y eliminar elementos usando los métodos `append` y `pop_front`.

Tanto si eres un programador experimentado como si acabas de empezar con Cairo 1.0, comprender cómo trabajar con arrays es una habilidad esencial que te ayudará a crear programas más complejos. ¡Entonces empecemos!

## Definición de un Array

En programación, un array es una estructura de datos que almacena una secuencia de tamaño fijo de elementos del mismo tipo. Se puede acceder a los elementos de un array por su índice o posición en el array. En Cairo 1.0, puede definir un array mediante un trait (rasgo en español) denominado `ArrayTrait<T>`.

Aquí hay un ejemplo de cómo definir un trait de array en Cairo 1.0:

```cairo
trait ArrayTrait<T> {
    fn new() -> Array<T>;
    fn append(ref self: Array<T>, value: T);
    fn pop_front(ref self: Array<T>) -> Option<T> nopanic;
    fn get(self: @Array<T>, index: usize) -> Option<Box<@T>>;
    fn at(self: @Array<T>, index: usize) -> @T;
    fn len(self: @Array<T>) -> usize;
    fn is_empty(self: @Array<T>) -> bool;
    fn span(self: @Array<T>) -> Span<T>;
}
```
Este trait define varios métodos que se pueden usar para crear, manipular y acceder a arrays. El método `new` crea un array nuevo, mientras que `append` agrega un nuevo elemento al final de un array existente. El método `pop_front` elimina y devuelve el primer elemento del array, mientras que `get` y `at` recupera un elemento en un índice específico del array. El método `len` devuelve el número de elementos de un array, mientras que `is_empty` comprueba si hay un array vacío. Finalmente, el método span devuelve un objeto `Span<T>` que se puede usar para representar una instantánea de un array.

## Creación y modificación de un Array

### Crear un Array

Para crear un array en Cairo 1.0, primero debemos definir un type concreto que implemente el trait `ArrayTrait<T>`. Esto se puede hacer usando la palabra clave `impl`, como se muestra a continuación:

```cairo
impl ArrayImpl<T> of ArrayTrait::<T> {
  // Implementation of ArrayTrait methods goes here
}
```
Cairo 1.0 ha definido un type de array concreto predeterminado, podemos crear una nueva instancia de array utilizando el método `new` proporcionado por el trait `ArrayTrait<T>`, como se muestra a continuación:

```cairo
let mut my_array = ArrayTrait::new();
```
Esto creará un nuevo array vacío de tipo `T`, donde `T` es el tipo de parámetro para nuestro array de type concreto.

### Agregar elementos a un Array

Una vez que hemos creado un array, podemos agregarle nuevos elementos usando el método `append` proporcionado por el trait `ArrayTrait<T>`. Este método toma una referencia a la instancia del array y el valor para agregar como argumentos, y agrega el valor al final del array. Aquí hay un ejemplo de cómo usar el método `append` para agregar elementos a un array:

```cairo
my_array.append(1);
my_array.append(2);
my_array.append(3);
```
Esto agregará los valores `1`, `2` y `3` al final del array.

### Eliminación de elementos de un Array

Para eliminar elementos de un array en Cairo 1.0, podemos usar el método `pop_front` proporcionado por el trait `ArrayTrait<T>`. Este método elimina el primer elemento de la matriz y lo devuelve como `Option<T>`. Si el array está vacío, `pop_front` devuelve `None`. Aquí hay un ejemplo de cómo usar el método `pop_front` para eliminar elementos de un array:

```cairo
let first_element = my_array.pop_front();
```
Esto eliminará el primer elemento del array y lo almacenará en la variable `first_element`. Si el array está vacío, `first_element` será `None`.

### Acceso a un Array

Acceder a los elementos de un array en Cairo 1.0 es simple y directo. El método `at` se puede usar para obtener una referencia a un elemento del array en un índice dado. Aquí hay un ejemplo:

```cairo
use array::ArrayTrait;
let arr = ArrayTrait::new();
arr.append(10);
arr.append(20);
// Access the first element using the `at` method
let first = *arr.at(0);
// Access the second element using the `at` method
let second = *arr.at(1);
```

El método `get` también se puede usar para acceder a un elemento del array, pero devuelve un type `Option` que puede contener el elemento o puede ser `None` si el índice está fuera de los límites. Aquí hay un ejemplo:

```cairo
use array::ArrayTrait;
use option::OptionTrait;

let arr = ArrayTrait::new();
arr.append(10);
arr.append(20);
// Get the first element using the `get` method
let first = arr.get(0).unwrap();
// Get the second element using the `get` method
let second = arr.get(1).unwrap();
```
## Conclusión

En resumen, Cairo 1.0 es un lenguaje poderoso que proporciona una implementación robusta de matrices a través del trait `ArrayTrait`, lo que facilita la creación y manipulación de matrices en sus programas.



