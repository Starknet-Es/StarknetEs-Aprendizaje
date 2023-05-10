<div align="center">
  <h1 style="font-size: larger;">
    <strong>Explorando Cairo 1.0: Un lenguaje de alto nivel similar a Rust para programas demostrables (6)</strong> 
    </h1>  
    <img src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/Explorando_Cairo1_pt6.png" width="600">

_[Artículo Original](https://medium.com/@0xasten/exploring-cairo-1-0-a-rust-like-high-level-language-for-provable-programs-6-afcfdff1afea)_ - Realizado por el compañero [Asten](https://twitter.com/0xasten)
</div>

Cairo 1.0 es un lenguaje de alto nivel diseñado para crear programas comprobables para computación general. Tiene una sintaxis similar a Rust, lo que lo convierte en una buena opción para los programadores que ya están familiarizados con Rust. En esta publicación, exploraremos algunos de los conceptos clave en Cairo 1.0, incluida la mutabilidad variable, la propiedad, el préstamo y las referencias.

## Mutabilidad

En Cairo 1.0, las variables se pueden declarar como mutables o inmutables usando la palabra clave `mut`. Las variables inmutables son de solo lectura y no se pueden modificar una vez que se inicializan. Las variables mutables, por otro lado, se pueden modificar después de inicializarlas.

```cairo
fn main() {
    let arr0 = ArrayTrait::new();
    let mut arr1 = ArrayTrait::new();
}
```
En los fragmentos de código proporcionados, podemos ver el uso de variables mutables e inmutables en la declaración de las palabras clave `let` y `mut`. Por ejemplo, la variable `arr0` se declara inmutable mientras que `arr1` se declara mutable usando `mut`.

## Ownership (Propiedad, en español)

El ownership es otro concepto crucial en Cairo 1.0. Cada valor en Cairo 1.0 tiene un ownership, que es la variable que contiene el valor. El ownership es importante porque determina la vida útil de un valor. El ownership de un valor es responsable de desasignar el valor cuando ya no se necesita. Cuando se pasa un valor a una función, el ownership del valor se transfiere a la función. Si la función devuelve el valor, el ownership se vuelve a transferir a la persona que llama.

Por ejemplo, en el siguiente código, el ownership de `arr0` se transfiere a `fill_arr` cuando se pasa como parámetro y el ownership del `arr` devuelto se transfiere a `arr1`:

```cairo
use array::ArrayTrait;

fn main() {
    let arr0 = ArrayTrait::new();
    let mut arr1 = fill_arr(arr0);
    arr1.append(88);
    arr1.span().snapshot.clone().print();
}
fn fill_arr(arr: Array<felt252>) -> Array<felt252> {
    let mut arr = arr;
    arr.append(22);
    arr
}
```

## Borrowing (Préstamo, en español)

El borrowing es una técnica utilizada para dar acceso temporal a un valor propio de otra variable sin transferir el ownership. Esto es útil cuando un valor debe usarse en varios lugares pero el ownership no se puede transferir.

Por ejemplo, en el código siguiente, `arr0` se toma prestado de `fill_arrayas` como parámetro de referencia:

```cairo
use array::ArrayTrait;
use debug::PrintTrait;

fn main() {
    let mut arr0 = ArrayTrait::new();
    fill_array(ref arr0);
    arr0.print();
}

fn fill_array(ref arr: Array<felt252>) {
    arr.append(22);
}
```

En Cairo 1.0, las referencias se representan mediante la palabra clave `ref`, que se utiliza para indicar que un parámetro de función debe pasarse por referencia. Por ejemplo, en el siguiente código:

```cairo
fn pass_by_ref(ref arr: Array<felt252>) {}
```

la función `pass_by_ref` toma una referencia a un `Array` de valores `felt252`. Esto significa que `pass_by_ref` puede acceder a la estructura de datos original sin necesidad de crear una copia de la misma.

Otra forma de trabajar con referencias en Cairo 1.0 es usar @ para crear una instantánea de una estructura de datos. Las instantáneas son referencias de solo lectura que permiten que una función acceda a la estructura de datos original sin el riesgo de modificarla. Por ejemplo, en el siguiente código:

```cairo
fn pass_by_snapshot(x: @Array<felt252>) {}
```

la función `pass_by_snapshot` toma una instantánea de un `Array` de valores de `felt252`. Esto significa que `pass_by_snapshot` puede acceder a la estructura de datos, pero no puede modificarla.

Los borrowings y las referencias son conceptos importantes en Cairo 1.0 porque permiten a los programadores pasar datos entre funciones sin necesidad de copiarlos. Esto puede ser útil cuando se trabaja con grandes estructuras de datos o cuando el rendimiento es una preocupación.

En conclusión, el lenguaje Cairo 1.0 proporciona a los desarrolladores varios conceptos para manejar la mutabilidad variable, la propiedad, el préstamo y las referencias. Estos conceptos son esenciales para crear programas comprobables para computación general.

