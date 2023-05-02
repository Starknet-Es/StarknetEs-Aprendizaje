<div align="center">
  <h1 style="font-size: larger;">
    <strong>Explorando Cairo 1.0: Un lenguaje de alto nivel similar a Rust para programas demostrables (3)</strong> 
    </h1>  
    <img src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/Explorando_Cairo1_pt3.png" width="600">

_[Artículo Original](https://medium.com/dev-genius/exploring-cairo-1-0-a-rust-like-high-level-language-for-provable-programs-3-853a44410832)_ - Realizado por el compañero [Asten](https://twitter.com/0xasten)
</div>

En este artículo, exploraremos algunas características clave de Cairo 1.0, incluyendo el flujo de control para expresiones if, los enumerados con valores asociados, las expresiones de coincidencia y la opción.

## Flujo de control para expresiones If
En Cairo 1.0, las expresiones **`if`** se pueden utilizar para el flujo de control. La sintaxis de las expresiones **`if`** es similar a la de Rust, donde la condición está rodeada por paréntesis y el cuerpo está rodeado por llaves.

Aquí hay un ejemplo de una expresión **`if`** utilizada en la función **`foo_if_fizz**`:

```cairo
fn foo_if_fizz(fizzish: felt252) -> felt252 {
    if fizzish == 'fizz' {
        'foo'
    } else if fizzish == 'fuzz' {
        'bar'
    } else {
        'baz'
    }
}
```

En este ejemplo, la expresión **`if`** verifica si **`fizzish`** es igual a **`'fizz'`**. Si lo es, la expresión devuelve **`'foo'`**. Si no lo es, verifica si **`fizzish`** es igual a **`'fuzz'`**. Si es así, la expresión devuelve **`'bar'`**. De lo contrario, la expresión devuelve **`'baz'`**.

## Enumeraciones con Valores Asociados
En Cairo 1.0, se pueden definir enumeraciones que tienen valores asociados de diferentes tipos. Esto permite crear tipos de datos más flexibles y expresivos.

Por ejemplo, en la siguiente definición de enumeración, cada variante tiene un valor asociado de un tipo diferente:

```cairo
#[derive(Drop, Copy)]
struct Point {
    x: u8,
    y: u8,
}

enum Message {
    ChangeColor:((u8, u8, u8)),
    Echo:(felt252),
    Move:(Point),
    Quit:(()),
}
```

Aquí, la variante **`ChangeColor`** tiene un valor asociado de tipo **`(u8, u8, u8)`**, que representa un color RGB. La variante **`Echo`** tiene un valor asociado de tipo **`felt252`**, que es un tipo de cadena personalizado. La variante **`Move`** tiene un valor asociado de tipo **`Point`**, que es un tipo de estructura personalizado que representa un punto 2D. Por último, la variante **`Quit`** no tiene valor asociado, representado por el tipo de unidad **`()`**.

## Las expresiones de coincidencia (Match)
Cairo 1.0 proporciona una construcción poderosa de control de flujo conocida como expresiones de coincidencia **`match`**. La expresión **`match`** le permite comparar un valor con una serie de patrones y ejecutar el bloque de código correspondiente para el primer patrón que coincida.

En el siguiente ejemplo, utilizamos una expresión **`match`** para determinar el valor de una moneda:

```cairo
enum Coin {
    Penny:(()),
    Nickel:(()),
    Dime:(()),
    Quarter:(()),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny(()) => 1_u8,
        Coin::Nickel(()) => 5_u8,
        Coin::Dime(()) => 10_u8,
        Coin::Quarter(()) => 25_u8,
    }
}
```

La expresión **`match`** en este ejemplo toma un valor **`coin`** de tipo **`Coin`** y lo compara con cuatro posibles patrones. Si la **`coin`** coincide con uno de los patrones, se devuelve el valor correspondiente.

## Definición de Option
En Cairo 1.0, el tipo **`Option`** se utiliza para representar valores que pueden o no estar presentes. Es similar al tipo **`Option`** de Rust y puede ser **`Some`** o **`None`**. Cuando hay un valor presente, se envuelve en una variante **`Some`**, y cuando está ausente, se envuelve en una variante **`None`**.

Para comprobar si un **`Option`** contiene un valor o no, Cairo 1.0 proporciona dos métodos: **`is_some`** e **`is_none`**. El método **`is_some`** devuelve true si el **`Option`** contiene un valor y **`false`** en caso contrario. Por otro lado, el método **`is_none`** devuelve true si el **`Option**` no contiene un valor y **`false`** en caso contrario.

Aquí hay un ejemplo de cómo utilizar **`Option`**, **`is_some`** e **`is_none`** en Cairo 1.0:

```cairo
use option::OptionTrait;
use debug::PrintTrait;

fn main() {
    let some_value: Option<u8> = Some(1_u8);
    let none_value: Option<u8> = None(());
    if some_value.is_some() {
        ('The value is present').print();
    } else {
    ('The value is not present').print();
    }
    if none_value.is_none() {
    ('The value is not present').print();
    } else {
        ('The value is present').print();
    }
}
```

En el ejemplo anterior, creamos dos valores **`Option<u8>`**, uno con un valor de 1 y otro sin valor. Luego usamos los métodos **`is_some`** y **`is_none`** para verificar si los valores están presentes o no, e imprimimos un mensaje en consecuencia.

En general, **`Option`**, **`is_some`** y **`is_none`** son herramientas poderosas para tratar con valores potencialmente ausentes en Cairo 1.0, lo que le permite escribir programas demostrables que son robustos y confiables.

Al usar Cairo 1.0, podemos estar seguros de que nuestros programas funcionarán como se pretende, sin efectos secundarios no deseados o vulnerabilidades de seguridad. Entonces, pruebe Cairo 1.0 y vea cómo puede ayudarlo a escribir software más seguro y demostrable.
