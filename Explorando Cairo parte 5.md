<div align="center">
  <h1 style="font-size: larger;">
    <strong>Explorando Cairo 1.0: Un lenguaje de alto nivel similar a Rust para programas demostrables (5)</strong> 
    </h1>  
    <img src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/Explorando_Cairo1_pt5.png" width="600">

_[Artículo Original](https://blog.devgenius.io/exploring-cairo-1-0-a-rust-like-high-level-language-for-provable-programs-5-9b9077191747)_ - Realizado por el compañero [Asten](https://twitter.com/0xasten)
</div>

En este artículo, exploraremos el concepto de los Traits y Structs (Características/Rasgos y Estructuras, en español) en el contexto del lenguaje Cairo 1.0, un lenguaje de alto nivel diseñado para crear programas comprobables para la computación general. Profundizaremos en cómo funcionan los Traits y Structs en Cairo 1.0, y cómo se pueden utilizar para escribir un código robusto y mantenible.

## Definición e instancia de Structs

Los structs son un tipo de datos fundamental en Cairo 1.0, utilizados para agrupar datos relacionados. 

En Cairo 1.0, podemos definir una estructura usando la palabra clave `struct` seguida del nombre del struct y sus fields (campos, en español). Los fields pueden ser de cualquier tipo válido de Cairo 1.0. Por ejemplo, así es como podemos definir un struct llamado `ColorStruct` con tres fields de tipo `felt252`:

```cairo
#[derive(Copy, Drop)]
struct ColorStruct {
    red: felt252,
    green: felt252,
    blue: felt252,
}
```
En el código anterior, hemos definido una struct denominado `ColorStruct` con tres fields: `red`, `green` y `blue`. Cada field es de tipo `felt252`, que es un tipo de entero integrado en Cairo 1.0.

Podemos instanciar un struct en Cairo 1.0 usando el nombre del struct y proporcionando los valores para sus fields. Por ejemplo, así es como podemos crear una instancia de `ColorStruct` con algunos valores:

```cairo
let green = ColorStruct {red: 0, green: 255, blue: 0};
```

En el código anterior, hemos creado un nuevo `ColorStruct` llamado `green` con los valores `red = 0`, `green = 255` y `blue = 0`.

## Destrucción de Structs

Para acceder a los datos dentro de un Struct, puede usar un proceso llamado "destruction", que consiste en desarmar el struct y asignar sus valores a nuevas variables.

En el siguiente ejemplo, tenemos una estructura llamada `Order` (pedido, en español), que representa un pedido realizado por un cliente. La función `create_order_template()` crea una nueva instancia de `Order` con algunos valores predeterminados.

```cairo
#[derive(Copy, Drop)]
struct Order {
    name: felt252,
    year: felt252,
    made_by_phone: bool,
    made_by_mobile: bool,
    made_by_email: bool,
    item_number: felt252,
    count: felt252,
}

fn create_order_template() -> Order {
    Order {
        name: 'Bob',
        year: 2019,
        made_by_phone: false,
        made_by_mobile: false,
        made_by_email: true,
        item_number: 123,
        count: 0
    }
}
```
Para destruir el struct `Order`, podemos usar la siguiente sintaxis:

```cairo
let order_template = create_order_template();
let Order {name, year, made_by_phone, made_by_mobile, made_by_email, item_number, count} = order_template;
```

## Definición de Trait

Un Trait es una colección de métodos que pueden ser implementados por múltiples types. En Cairo 1.0, los traits se definen mediante la palabra clave `trait`, seguida del nombre del trait y una lista de métodos que debe implementar cualquier type que implemente el rasgo. Aquí hay un ejemplo:`

```cairo
trait AnimalTrait<T> {
    fn new() -> T;
    fn make_noise(self: T) -> felt252;
}
```

Este Trait se llama `AnimalTrait` y tiene dos métodos: `new`, que crea una nueva instancia del type que implementa el Trait, y `make_noise`, que devuelve un valor `felt252` que representa el ruido que hace el animal.

La sintaxis `<T>` después del nombre del Trait significa que `T` es un parámetro de tipo genérico, lo que permite que varios types implementen el Trait. 

## Implementación de Traits

Para implementar un Trait para un type específico, usamos la palabra clave `impl`, seguida del nombre del Trait y el nombre del type para el que lo estamos implementando. Aquí hay un ejemplo:

```cairo
#[derive(Copy, Drop)]
struct Cat {
    noise: felt252,
}

impl CatImpl of AnimalTrait::<Cat> {
    fn new() -> Cat {
        Cat { noise: 'meow' }
    }
    fn make_noise(self: Cat) -> felt252 {
        self.noise
    }
}
```
Este código implementa el trait `AnimalTrait` para el type `Cat`. Definimos una nueva implementación llamada `CatImpl` y especificamos que está implementando `AnimalTrait` para `Cat` usando la sintaxis de `AnimalTrait::<Cat>`. Luego definimos los dos métodos requeridos por el Trait: `new`, que devuelve una nueva instancia de `Cat` con el ruido establecido en `"meow"`, y `make_noise` que simplemente devuelve el valor de `self.noise`.

## Implementar un Trait sin sintaxis `<T>`

Echemos un vistazo al siguiente fragmento de código:

```cairo
#[derive(Copy, Drop)]
struct Package {
    sender_country: felt252,
    recipient_country: felt252,
    weight_in_grams: usize,
}

trait PackageTrait {
    fn new(sender_country: felt252, recipient_country: felt252, weight_in_grams: usize) -> Package;
    fn is_international(ref self: Package) -> bool;
    fn get_fees(ref self: Package, cents_per_gram: usize) -> usize;
}
```

Aquí, definimos una estructura `Package` que representa un paquete que se envía de un país a otro. También definimos un trait `PackageTrait` que define tres funciones: `new`, `is_international` y `get_fees`. Estas funciones nos permitirán adjuntar lógica a nuestro struct `Package`.

```cairo
impl PackageImpl of PackageTrait {
    fn new(sender_country: felt252, recipient_country: felt252, weight_in_grams: usize) -> Package {
        if weight_in_grams <= 0_usize {
            let mut data = ArrayTrait::new();
            data.append('x');
            panic(data);
        }
        Package { sender_country, recipient_country, weight_in_grams,  }
    }
    
    fn is_international(ref self: Package) -> bool
    {
        self.sender_country != self.recipient_country
    }
    
    fn get_fees(ref self: Package, cents_per_gram: usize) -> usize
    {
        self.weight_in_grams * cents_per_gram
    }
}
```
En esta implementación, definimos las tres funciones del trait `PackageTrait`. La función `new` crea un nuevo struct de `Package` y verifica que el peso sea mayor que 0. La `función is_international` verifica si el paquete se envía internacionalmente o no. La función `get_fees` calcula las tarifas de envío del paquete en función de su peso y el costo por gramo.

Adjuntar lógica a una estructura en Cairo 1.0 se realiza mediante el uso de traits e implementaciones. Al hacerlo, podemos crear estructuras de datos más complejas y capaces mientras mantenemos la demostrabilidad y confiabilidad de nuestros programas.

Mediante el uso de estos conceptos, los programadores pueden crear código reutilizable y mantenible que se puede modificar y ampliar fácilmente según sea necesario.


