<div align="center">
  <h1 style="font-size: larger;">
    <strong>Explorando Cairo 1.0: Un lenguaje de alto nivel similar a Rust para programas demostrables (1)</strong> 
    </h1>  
    <img src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/Explorando_Cairo1_pt1.png" width="600">

_[Artículo Original](https://medium.com/dev-genius/exploring-cairo-1-0-a-rust-like-high-level-language-for-provable-programs-1-ea6d51cfa0fe)_ - Realizado por el compañero [Asten](https://twitter.com/0xasten)
</div>

Cairo 1.0 es un lenguaje de programación de alto nivel utilizado para crear programas comprobables para computación general. Está escrito en Rust y su sintaxis es algo similar a Rust. En este artículo, analizaremos algunas de las características clave de Cairo 1.0, incluida la declaración de variables y constantes, la variabilidad de variables, la declaración de funciones, el retorno de funciones, la operación aritmética de felt252, el type booleano, string corto, la tupla y la destrucción de tuplas.

## Declaración de variables

Cairo 1.0 admite la declaración de variables, lo que le permite crear variables y asignarles valores. Por ejemplo, el siguiente código declara una variable `x` y le asigna el valor `10`:

```cairo
let x = 10;
```

## Declaración de constantes

Además de las variables, Cairo 1.0 también admite la declaración de constantes, lo que le permite crear constantes que no se pueden cambiar. Por ejemplo, el siguiente código declara un NÚMERO (`NUMBER`) constante y le asigna el valor `3`:

```cairo
const NUMBER: felt252 = 3;
```

## Variabilidad de Variables

En Cairo 1.0, puede usar la palabra clave `mut` para declarar una variable como mutable, lo que significa que su valor se puede cambiar. Por ejemplo, el siguiente código declara una variable mutable `y` y le asigna el valor `1`, y luego cambia su valor a `5`:

```cairo
let mut y: felt252 = 1;
y = 5;
```

## Declaración de funciones

Cairo 1.0 admite la declaración de funciones, lo que permite crear funciones que se pueden llamar desde otras partes de su programa. Por ejemplo, el siguiente código declara dos funciones `add` y `sub` que toman dos argumentos de tipo `felt252` y devuelven un valor de tipo `felt252`:

```cairo
fn add(a: felt252, b: felt252) -> felt252 {
    a + b
}

fn sub(a: felt252, b: felt252) -> felt252 {
    a - b
}
```

## Retorno de funciones

En Cairo 1.0, puedes usar el operador `->` para especificar el tipo de retorno de una función. Por ejemplo, las funciones `add` y `sub` declaradas anteriormente devuelven un valor de tipo `felt252`.

## Operaciones aritméticas con felt252

Cairo 1.0 admite operaciones aritméticas con el tipo de datos `felt252`. Por ejemplo, la función `add` declarada arriba toma dos argumentos de tipo `felt252` y devuelve su suma.

## Tipo de dato Booleano

Cairo 1.0 admite el tipo de dato booleano, que representa valores verdaderos o falsos (true or false). Por ejemplo, el siguiente código declara una variable booleana `is_bool` y le asigna el valor `true`:

```cairo
let is_bool = true;
```

## Strings cortos

En Cairo 1.0, puede usar strings cortos para representar strings (cadenas) que tienen como máximo 31 caracteres. Los strings cortos son en realidad felts, no son strings reales. Para crear un string corto, encierre los caracteres entre comillas simples. Por ejemplo, el siguiente código declara una variable `sstring` mutable y le asigna el valor `'C'`, que es un string corto:

```cairo
let mut sstring = 'C';
```

## Tuplas y destrucción de tuplas

Cairo 1.0 admite tuplas, que son grupos de valores de diferentes tipos. Por ejemplo, el siguiente código declara una tupla `cat` que contiene un `string` y un número entero:

```cairo
let cat = ('Furry McFurson', 3);
let (name, age) = cat;
```

Puede desestructurar una tupla utilizando la coincidencia de patrones. En el código de ejemplo, la tupla `cat` se desestructura en sus dos componentes, `name` y `age` (nombre y edad), usando la sintaxis `let (name, age) = cat`;. Esto le permite acceder a los valores individuales en la tupla y usarlos por separado en su código.

En resumen, Cairo 1.0 es un poderoso lenguaje de alto nivel que se puede usar para crear programas comprobables para computación general. Admite una variedad de tipos de datos y funciones, incluidas declaraciones de variables y constantes, declaraciones y devoluciones de funciones, operaciones aritméticas con felt252, tipos booleanos, strings cortos, tuplas y desestructuración de tuplas. Su sintaxis es similar a Rust, lo que lo hace accesible para los desarrolladores que ya están familiarizados con Rust.


