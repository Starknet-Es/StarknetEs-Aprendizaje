<div align="center">
<img alt="starknet logo" src="https://github.com/Nadai2010/DimeYad/blob/master/Sierra2.png" width="800" >
  <h1 style="font-size: larger;">
    <img src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/Sierra2.png" width="40">
    <strong>En el interior de Cairo 1.0: Explorando Sierra</strong> 
    <img src="https://github.com/Nadai2010/Nadai-SHARP-Starknet/blob/master/im%C3%A1genes/Starknet.png" width="40">
  </h1>
</div>


## Parte 2: Anatomía de los programas Sierra.

## Introducción
_Traducción del articulo de [Eni.Stark](https://twitter.com/eniwhere_) para Nethermind_ 

En nuestra publicación de [blog anterior](), presentamos Sierra y discutimos cómo simplifica el proceso de desarrollo de contratos de Starknet. En esta publicación profundizaremos en el código de Sierra y exploraremos sus diversas características, incluidos los tipos de datos, las funciones de biblioteca, las declaraciones de programa y las funciones definidas por el usuario. Nuestro objetivo es comprender exhaustivamente el código de Sierra y lo que lo convierte en una representación intermedia segura del código de Cairo. Familiarizarse con Sierra es opcional para escribir programas de Cairo, pero ayuda a comprender cómo funcionan juntos los programas y las pruebas.

## Análisis de un programa Sierra simple
Para comprender mejor la estructura de Sierra, examinaremos un programa Sierra simple antes de avanzar hacia un código más complejo. El siguiente código, escrito en Cairo 1.0, se compila a Sierra utilizando el comando `cairo-compile program.cairo program.sierra -r`. Es una función sencilla que devuelve una variable de tipo `felt252` con el valor 1.

```cairo
fn main() -> felt252 {
    1
}
```

Un programa Sierra consta de cuatro partes distintas, separadas por líneas vacías.

La primera parte de un programa Sierra involucra la declaración de los tipos utilizados en el programa. Durante la compilación de un programa Cairo a Sierra, se asigna un ID único a cada tipo utilizado. Este ID se reutiliza posteriormente en el programa para identificar el tipo de variables esperadas en las declaraciones de funciones. Las declaraciones de tipos se escriben con la sintaxis `type my_id = concrete_type`, donde `concrete_type` es un tipo definido en el núcleo de Sierra. Un ejemplo común es la declaración del tipo `felt252`, con `type felt252 = felt252`

La siguiente parte de un programa Sierra consiste en listar las libfuncs utilizadas. Las libfuncs son funciones incorporadas definidas en el compilador Sierra que pueden ser compiladas en código CASM. En este paso, cada libfunc utilizada en el programa debe ser definida junto con el tipo de entrada que espera. La sintaxis utilizada para la declaración de libfunc es `libfunc my_id = function<T>`. Por ejemplo, la `libfunc drop<T>` puede ser usada con el tipo `felt252`, y el tipo `NonZero<felt252>`, resultando en la declaración de ambas `libfunc drop_felt = drop<felt252>` y `libfunc drop_nz_felt = drop<NonZero<felt252>>`.

La tercera parte de un programa Sierra consiste en declarar las sentencias que componen el código del programa y especificar su comportamiento previsto. Estas sentencias se ejecutan secuencialmente y pueden invocar una libfunc previamente declarada o devolver una variable. La sintaxis para declarar estas sentencias es la siguiente: `<libfunc_id>(<input variables>) -> (<output variables>)` or `return(<variable_id>)`

Finalmente, la última parte consiste en declarar las funciones definidas por el usuario que se utilizan dentro del programa. A estas funciones se les asigna un ID único y se les asocia un índice correspondiente a la sentencia donde comienza su ejecución. La firma de cada función especifica los parámetros de entrada, sus tipos y los tipos de la variable devuelta. El formato es el siguiente: `function_id@statement_index(<param_names: types>) -> (<return types>);`.

Por ejemplo, considere la declaración de una función llamada `fib`, que comienza en el índice 0, toma tres entradas de tipo `felt252`, y devuelve una única salida de tipo `felt252`: `fib@0(a: felt252, b: felt252, n: felt252)->felt252`

Compilando el código Cairo de antes se obtiene el siguiente código Sierra:

```cairo
fn main() -> felt252 {
    1
}
```
```cairo
type felt252 = felt252;

libfunc felt_const<1> = felt_const<1>;
libfunc store_temp<felt252> = store_temp<felt252>;

felt_const<1>() -> ([0]);
store_temp<felt252>([0]) -> ([1]);
return([1]);

program::program::main@0() -> (felt252);
```

El código dado puede interpretarse como sigue: "El programa utiliza un único tipo de datos, `felt252`. Utiliza dos funciones de biblioteca - `felt_const<1>`, que devuelve la constante felt252 `1`, y `store_temp<felt252>`, que pone un valor constante en memoria. El programa tiene una función principal que comienza en la sentencia 0 y devuelve una variable de tipo `felt252`. Durante la ejecución de mi programa, llamamos a la libfunc `felt_const<1>` para crear una variable con id `[0]`. Pasamos esta variable a memoria y recuperamos otra variable de id `[1]`, que devuelvo al final de la función."

## De código fallido a código ramificado
Como se discutió en el post anterior, si eliminamos todas las operaciones fallidas de la semántica de Sierra, podemos probar la ejecución de cada transacción, independientemente del resultado de la ejecución. Esto permite la inclusión de transacciones fallidas en bloques, permitiendo a los secuenciadores recibir un pago por su trabajo. Los contratos inteligentes frecuentemente requieren verificar si un usuario está autorizado a realizar una acción específica como acuñar o transferir tokens. Esta verificación se realiza utilizando sentencias assert para comprobar condiciones booleanas. La biblioteca central de Cairo 1 implementa `assert` como una función que puede entrar en pánico si la condición no se cumple.

Para simplificar, veremos un programa Cairo que puede entrar en pánico antes de terminar su ejecución y veremos cómo se compila a Sierra.

```cairo
use array::ArrayTrait;
fn main() -> felt252 {
    let a = 1;
    if (a == 0) {
        panic(ArrayTrait::<felt252>::new());
    }
    let b = 2;
    a + b
}
```
```cairo
type felt252 = felt252;
type NonZero<felt252> = NonZero<felt252>;
type Array<felt252> = Array<felt252>;
type Tuple<felt252> = Struct<ut@Tuple, felt252>;
type core::PanicResult::<(core::felt252,)> = Enum<ut@core::PanicResult::<(core::felt252,)>, Tuple<felt252>, Array<felt252>>;

libfunc felt_const<1> = felt_const<1>;
libfunc store_temp<felt252> = store_temp<felt252>;
libfunc dup<felt252> = dup<felt252>;
libfunc felt_is_zero = felt_is_zero;
libfunc branch_align = branch_align;
libfunc drop<felt252> = drop<felt252>;
libfunc array_new<felt252> = array_new<felt252>;
libfunc enum_init<core::PanicResult::<(core::felt252,)>, 1> = enum_init<core::PanicResult::<(core::felt252,)>, 1>;
libfunc store_temp<core::PanicResult::<(core::felt252,)>> = store_temp<core::PanicResult::<(core::felt252,)>>;
libfunc drop<NonZero<felt252>> = drop<NonZero<felt252>>;
libfunc felt_const<2> = felt_const<2>;
libfunc felt_add = felt_add;
libfunc struct_construct<Tuple<felt252>> = struct_construct<Tuple<felt252>>;
libfunc enum_init<core::PanicResult::<(core::felt252,)>, 0> = enum_init<core::PanicResult::<(core::felt252,)>, 0>;

0. felt_const<1>() -> ([0]);
1. store_temp<felt252>([0]) -> ([0]); // Push the value `1` to memory
2. dup<felt252>([0]) -> ([0], [2]); // Because `felt_is_zero` consumes our value, we
// duplicate it to use it later in the code.
3. felt_is_zero([2]) { fallthrough() 10([1]) };
4. branch_align() -> ();
5. drop<felt252>([0]) -> ();
6. array_new<felt252>() -> ([3]);
7. enum_init<core::PanicResult::<(core::felt252,)>, 1>([3]) -> ([4]);
8. store_temp<core::PanicResult::<(core::felt252,)>>([4]) -> ([5]);
9. return([5]);
10. branch_align() -> ();
11. drop<NonZero<felt252>>([1]) -> ();
12. felt_const<2>() -> ([6]);
13. felt_add([0], [6]) -> ([7]);
14. struct_construct<Tuple<felt252>>([7]) -> ([8]);
15. enum_init<core::PanicResult::<(core::felt252,)>, 0>([8]) -> ([9]);
16. store_temp<core::PanicResult::<(core::felt252,)>>([9]) -> ([10]);
17. return([10]);

examples::panic::main@0() -> (core::PanicResult::<(core::felt252,)>);
```

Destaquemos los conceptos más interesantes que Sierra demostró en este programa:
* La ausencia de una libfunc de `panic`, ya que el concepto de pánico como error de ejecución propio, no existe en los programas Sierra.
* La función de librería `felt_is_zero` usada en la sentencia #3 puede continuar con múltiples ramas después de la ejecución. En este caso particular, si la variable con ID `[2]` es cero, el código continuará con el caso `fallthrough` en la siguiente sentencia. Sin embargo, si la variable es distinta de cero, el programa pasará a la sentencia #10.
* La libfunc `branch_align` se utiliza para igualar los costes de gas y los cambios de ap a través de las rutas de fusión del código de ramificación.
* Se declara un tipo `core::PanicResult` en nuestro programa Sierra, indicando que nuestra función Cairo 1, que inicialmente devolvía `felt252`, ahora devuelve un `PanicResult`
* La libfunc `array_new` se utiliza para instanciar un nuevo array.
* Los enum se inicializan utilizando la libfunc `enum_init`.
* Las estructuras se construyen/deconstruyen utilizando las libfunc `struct_construct` y `struct_deconstruct`. Para acceder a un miembro de una estructura, primero se deconstruye la estructura en múltiples variables y luego se reconstruye cuando es necesario.

Durante la fase de bajada de compilación, el tipo de retorno de la función Cairo que contiene una sentencia de `panic` se convierte al tipo `PanicResult<T>`. Este nuevo tipo se pasa a todas sus funciones padre hasta que alcanza el punto de entrada del programa. La ejecución del programa se considera un fallo si se propaga un error de vuelta al punto de entrada.

```cairo
enum PanicResult<T> {
    Ok: T,
    Err: Array::<felt252>,
}
```
## Escribiendo nuestro propio programa Sierra
En esta parte, escribiremos nuestro propio programa Sierra para familiarizarnos con su sistema de tipos lineal, cómo manejar ramas en código Sierra, y obtener una mejor comprensión general de cómo funciona la pila Cairo.

Desarrollaremos un programa directo que calcule el factorial de un valor dado `n`, donde `n` es un felt252. El programa tomará `n` como entrada, que será un valor codificado en el código, y devolverá `n!` sin tener en cuenta los costes de gas por simplicidad. Antes de escribir las sentencias Sierra, declararemos todos los tipos necesarios, libfuncs y funciones definidas por el usuario.

### Definición de tipos
Nuestro programa necesitará `felts`. Como evaluaremos si n es igual a 0, también necesitamos declarar el tipo `NonZero<Felt252>`, un tipo envoltorio devuelto por la libfunc `felt_is_zero`.

```cairo
type felt252 = felt252;
type NonZeroFelt = NonZero<felt252>;
```

### Definición de libfuncs
Debido a que estamos escribiendo un programa con una función recursiva, el estado de `ap` al final de la ejecución del programa no se conoce en tiempo de compilación. Para ello, necesitaremos usar `disable_ap_tracking`. Necesitaremos `store_temp` para mover nuestros valores a ap antes de volver de una función, `branch_align` para igualar los cambios de ap a través de ramas condicionales, `function_call` para llamar a nuestra función recursiva definida por el usuario, `felt_const` para instanciar una constante felt252, `felt_sub` y `felt_add` para nuestras operaciones felt252, `dup<felt252>`, `drop<felt252>` y `drop<NonZeroFelt>` para duplicar y soltar variables donde sea necesario, y finalmente `felt_is_zero` para evaluar qué valor devolver. Aquí, declaramos la libfunc `felt_const` para devolver el valor `24`, que es la "entrada" de nuestro programa. Por último, también necesitamos la libfunc `rename<felt252>` para alinear las identidades para la fusión de control de flujo.

```cairo
libfunc disable_ap_tracking = disable_ap_tracking;
libfunc store_temp_felt = store_temp<felt252>;
libfunc branch_align = branch_align;
libfunc felt_const_24 = felt_const<24>;
libfunc felt_const_1 = felt_const<1>;
libfunc felt_sub = felt_sub;
libfunc felt_mul = felt_mul;
libfunc felt_is_zero = felt_is_zero;
libfunc dup_felt = dup<felt252>;
libfunc drop_felt = drop<felt252>;
libfunc drop_NonZeroFelt = drop<NonZeroFelt>;
libfunc multiply_rec_call = function_call<user@factorial::multiply_rec>;
libfunc rename_felt = rename<felt252>;
```

### Declarando funciones definidas por el usuario
Antes de escribir la parte central del código Sierra, podemos empezar declarando nuestras funciones. Necesitaremos la función `main`, que devuelve un `felt252`, y la función `multiply_rec`, que devuelve un `felt252`. En este punto, no sabemos el índice de la declaración donde comienza `multiply_rec` - llené este valor después de escribir el resto.

```cairo
factorial::main@0()->(felt252);
factorial::multiply_rec@6(n:felt252)->(felt252);
```

### Escribir las declaraciones Sierra
Ahora necesitamos escribir las declaraciones que dictan cómo se comporta nuestro programa. Comenzaremos con la función principal, que comienza en la sentencia #0. Nuestra función principal es simple: Necesitamos llamar a la función `multiply_rec` con un valor de entrada de `24` y devolver el resultado. Antes de llamar a una función definida por el usuario, tenemos que empujar el valor a la memoria utilizando el `store_temp` libfunc.

```cairo
disable_ap_tracking() -> ();
felt_const_24() -> (n);
store_temp_felt(n) -> (n_mem);
multiply_rec_call(n_mem) -> (result);
rename_felt(result) -> (final);
return(final);
```

A continuación podemos proceder con la función recursiva, que es más compleja, por lo que la dividiremos en tres partes.

En primer lugar, necesitamos evaluar si `n` es igual a cero con `felt_is_zero`. Dado que `n` debe utilizarse exactamente una vez, debemos duplicarlo para que esté disponible más adelante. Vale la pena notar que `dup` y `drop` libfuncs no generan código al compilar un programa Sierra a CASM. Sólo se usan a nivel de Sierra para cumplir con las restricciones del sistema de tipos lineal.

```cairo
disable_ap_tracking() -> ();
dup_felt(n) -> (n, n_);
felt_is_zero(n_) { fallthrough() 14(n_not_zero) };
```

Si la condición es verdadera, el programa ejecuta la siguiente declaracion a través de la rama `fallthrough`. En este caso, debemos eliminar todas las variables pendientes y devolver el valor `1`.

```cairo
branch_align() -> ();
drop_felt(n)->();
felt_const_1() -> (one);
store_temp_felt(one) -> (one_mem);
return(one_mem);
```

Si la condición es falsa, tenemos que calcular el valor de `n-1`, llamar a `multiply_rec`, multiplicar el resultado de la llamada a la función por `n` y devolverlo. La segunda rama de `felt_is_zero` comienza en la sentencia 14 y declara una variable `n_not_zero` que tenemos que eliminar porque no se utiliza.

```cairo
branch_align() -> ();
drop_NonZeroFelt(n_not_zero) -> ();
felt_const_1() -> (one);
dup_felt(n) -> (n,n_);
felt_sub(n_,one) -> (n_minus_one);
store_temp_felt(n_minus_one) -> (n_minus_one_mem);
multiply_rec_call(n_minus_one_mem) -> (call_result);
felt_mul(n,call_result) -> (intermediate_result);
store_temp_felt(intermediate_result) -> (intermediate_result_mem);
return(intermediate_result_mem);
```

El código Cairo correspondiente para el programa que acabamos de escribir en Sierra puro sería:

```cairo
fn main() -> felt252 {
    let n = 24;
    multiply_rec(n)
}

fn multiply_rec(n: felt252) -> felt252 {
    if (n == 0) {
        return 1;
    }
    n * multiply_rec(n - 1)
}
```

## Conclusión
En este post, exploramos la anatomía de un programa Sierra, analizando tanto un programa Sierra simple como uno más complejo que compila pánicos de Cairo 1 en código ramificado. También escribimos nuestro propio programa Sierra para entender mejor la pila de Cairo. Al final del post, aprendimos sobre los tipos, libfuncs, y funciones definidas por el usuario usadas en código Sierra, proveyendo los principios básicos para leer y entender programas Sierra. Puedes consultar esta documentación de Sierra para aprender más sobre las libfuncs existentes y su utilidad.

## Acerca de Nethermind
Nethermind es un equipo de constructores e investigadores de clase mundial. Capacitamos a empresas y desarrolladores de todo el mundo para acceder y construir sobre la web descentralizada. Nuestro trabajo toca cada parte del ecosistema web3, desde nuestro nodo Nethermind hasta la investigación criptográfica fundamental y la infraestructura para el ecosistema Starknet. Descubra nuestro conjunto de herramientas para Starknet: [Warp, el compilador de Solidity a Cairo;](https://nethermind.page.link/8a9f) [Voyager, un explorador de bloques de StarkNet;](https://nethermind.page.link/htwC) [Horus, la herramienta de verificación formal de código abierto para los contratos inteligentes de Starknet;](https://nethermind.io/horus/) [Juno, la implementación del cliente de Starknet,](https://github.com/NethermindEth/juno) y los [servicios de auditoría de seguridad de los contratos inteligentes de Cairo](https://nethermind.page.link/25ee).

Si estás interesado en resolver algunos de los problemas más difíciles de blockchain, [¡visita nuestra bolsa de trabajo!](https://nethermind.page.link/careers)

## Descargo de responsabilidad
Este artículo ha sido preparado para la información y comprensión general de los lectores, y no indica el respaldo de Nethermind a ningún activo, proyecto o equipo en particular, ni garantiza su seguridad. Ninguna representación o garantía, expresa o implícita, es dada por Nethermind en cuanto a la exactitud o integridad de la información u opiniones contenidas en el presente artículo. Ningún tercero debe confiar en este artículo de ninguna manera, incluyendo sin limitación como asesoramiento financiero, de inversión, fiscal, regulatorio, legal o de otro tipo, ni interpretar este artículo como cualquier forma de recomendación.
