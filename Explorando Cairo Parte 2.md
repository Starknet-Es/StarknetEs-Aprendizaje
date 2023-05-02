<div align="center">
  <h1 style="font-size: larger;">
    <strong>Explorando Cairo 1.0: Un lenguaje de alto nivel similar a Rust para programas demostrables (2)</strong> 
    </h1>  
    <img src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/Explorando_Cairo1_pt2.png" width="600">

_[Artículo Original](https://medium.com/dev-genius/exploring-cairo-1-0-a-rust-like-high-level-language-for-provable-programs-2-f26a47fad3f7)_ - Realizado por el compañero [Asten](https://twitter.com/0xasten)
</div>

En este artículo, exploraremos las operaciones de comparación y aritmética en los tipos de enteros y felt252, y la conversión de tipo entre los tipos de enteros y felt252 en Cairo 1.0. También demostraremos cómo escribir casos de prueba para estas operaciones para garantizar que nuestro código sea correcto.

## Tipos de enteros
Cairo 1.0 proporciona una variedad de tipos de enteros, incluyendo u8, u16, u32, u64, u128 y usize.

## Operaciones de comparación en tipos de enteros
En Cairo 1.0, los tipos de enteros admiten operaciones básicas de comparación, como menor que (<), menor o igual que (<=), mayor que (>), mayor o igual que (>=) y no igual a (!=). Estos operadores se pueden utilizar para comparar dos enteros del mismo tipo y devolver un valor booleano (verdadero o falso) dependiendo del resultado de la comparación. Por ejemplo:

```cairo
#[test]
fn test_u8_comparison() {
	assert(1_u8 < 4_u8, '1 < 4');
}
```

## Operaciones aritméticas en tipos de enteros
Cairo 1.0 admite operaciones aritméticas en tipos de enteros, incluyendo suma (+), resta (-), multiplicación (*), división (/) y módulo (%). Estos operadores se pueden utilizar para realizar operaciones aritméticas en dos enteros del mismo tipo y devolver un valor entero del mismo tipo que los operandos. Por ejemplo, para sumar dos enteros x e y de tipo u8, podemos utilizar el siguiente código:

```cairo
fn sum_u8s(x: u8, y: u8) -> u8 {
    x + y
}
```

Aquí hay un ejemplo de caso de prueba para operaciones de comparación y aritmética de enteros:

```cairo
#[test]
fn test_u8_operators() {
    assert(1_u8 == 1_u8, '1 == 1');
    assert(1_u8 != 2_u8, '1 != 2');
    assert(1_u8 + 3_u8 == 4_u8, '1 + 3 == 4');
    assert(3_u8 + 6_u8 == 9_u8, '3 + 6 == 9');
    assert(3_u8 - 1_u8 == 2_u8, '3 - 1 == 2');
    assert(1_u8 * 3_u8 == 3_u8, '1 * 3 == 3');
    assert(2_u8 * 4_u8 == 8_u8, '2 * 4 == 8');
    assert(19_u8 / 7_u8 == 2_u8, '19 / 7 == 2');
    assert(19_u8 % 7_u8 == 5_u8, '19 % 7 == 5');
    assert(231_u8 - 131_u8 == 100_u8, '231-131=100');
    assert(1_u8 < 4_u8, '1 < 4');
    assert(1_u8 <= 4_u8, '1 <= 4');
    assert(!(4_u8 < 4_u8), '!(4 < 4)');
    assert(4_u8 <= 4_u8, '4 <= 4');
    assert(5_u8 > 2_u8, '5 > 2');
    assert(5_u8 >= 2_u8, '5 >= 2');
    assert(!(3_u8 > 3_u8), '!(3 > 3)');
    assert(3_u8 >= 3_u8, '3 >= 3');
}
```

## Tipos de Felt y sus Operaciones
elt252 también soporta operaciones aritméticas básicas como suma, resta y multiplicación, y operadores de comparación como `<`, `<=`, `>` y `>=`.

Aquí hay un ejemplo de caso de prueba para las operaciones aritméticas y de comparación de felt252:

```cairo
#[test]
fn test_felt_operators() {
    assert(1 + 3 == 4, '1 + 3 == 4');
    assert(3 + 6 == 9, '3 + 6 == 9');
    assert(3 - 1 == 2, '3 - 1 == 2');
    assert(1231 - 231 == 1000, '1231-231=1000');
    assert(1 * 3 == 3, '1 * 3 == 3');
    assert(3 * 6 == 18, '3 * 6 == 18');
    assert(-3 == 1 - 4, '-3 == 1 - 4');
    assert(1 < 4, '1 < 4');
    assert(1 <= 4, '1 <= 4');
    assert(!(4 < 4), '!(4 < 4)');
    assert(4 <= 4, '4 <= 4');
    assert(5 > 2, '5 > 2');
    assert(5 >= 2, '5 >= 2');
    assert(!(3 > 3), '!(3 > 3)');
    assert(3 >= 3, '3 >= 3');
}
```

Cairo 1.0 también admite la conversión de tipos entre enteros y tipos felt252. Para convertir un tipo entero a un tipo felt252, podemos utilizar el método `.into()`. Por ejemplo, para convertir un entero x de tipo u8 a un tipo felt252, podemos utilizar el siguiente código:

```cairo
fn convert_to_felt(x: u8) -> felt252 {
    x.into()
}
```
Para convertir un tipo felt252 a un tipo entero, podemos utilizar el método **`.try_into()`**. Este método devuelve un tipo **`Result`** que puede ser **`Ok`** o **`Err`**. Para obtener el valor entero del variant **`Ok`**, podemos utilizar el método **`.unwrap()`**. Por ejemplo, para convertir un valor felt252 x a un valor entero de tipo u8, podemos utilizar el siguiente código:

```cairo
fn convert_felt_to_u8(x: felt252) -> u8 {
    x.try_into().unwrap()
}
```

## Escribir casos de prueba
Para asegurarnos de que nuestro código es correcto, debemos escribir casos de prueba para las operaciones de comparación y aritmética. En Cairo 1.0, podemos escribir casos de prueba usando el atributo **`#[test]`**. Por ejemplo, para probar la función **`sum_u8s`**, podemos escribir el siguiente caso de prueba:

```cairo
#[test]
fn test_sum_u8s() {
    assert(sum_u8s(1_u8, 2_u8) == 3_u8, 'Something went wrong');
}
```

Este caso de prueba afirma que el resultado de **`sum_u8s(1_u8, 2_u8)`** debe ser igual a 3_u8.

En conclusión, Cairo 1.0 proporciona un lenguaje de programación robusto y eficiente para la computación en general. El lenguaje admite una amplia gama de tipos enteros y proporciona operadores aritméticos y de comparación básicos.
