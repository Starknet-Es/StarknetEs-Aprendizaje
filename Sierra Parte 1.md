<div align="center">
<img alt="starknet logo" src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/Nethermind.png" width="800" >
  <h1 style="font-size: larger;">
    <img src="https://github.com/Nadai2010/Nadai-SHARP-Starknet/blob/master/im%C3%A1genes/Starknet.png" width="40">
    <strong>Bajo el capó de Cairo 1.0: Explorando Sierra</strong> 
    <img src="https://github.com/Nadai2010/Nadai-SHARP-Starknet/blob/master/im%C3%A1genes/Starknet.png" width="40">
  </h1>
</div>

## Parte 1: Una capa esencial para producir CASM seguro

## Introducción
_Traducción del articulo de [Eni.Stark](https://twitter.com/eniwhere_) para Nethermind_ 

Recientemente asistí a dos charlas en las Sesiones de Starkware sobre Sierra: [Enforcing Safety Using Typesystems por Shahar Papini](https://www.youtube.com/watch?v=-EHwaQuPuAASierra) y [Not Stopping at the Halting Problem](https://www.youtube.com/watch?v=wYxUedcdVZ4) por Ori Ziv. Te recomiendo encarecidamente que veas estos vídeos si quieres entender más sobre la pila Cairo. El siguiente post es el primero de una serie en la que me sumergiré en Sierra para comprender mejor Cairo, sus mecanismos y Starknet en general.

Sierra (Safe Intermediate Representation) es una capa intermedia entre el lenguaje de alto nivel Cairo y los objetivos de compilación como Cairo Assembly (CASM). El lenguaje está diseñado para garantizar la seguridad y evitar errores en tiempo de ejecución. Garantiza que todas las funciones retornen y que no haya bucles infinitos mediante un compilador capaz de detectar operaciones que podrían fallar en tiempo de compilación. Sierra utiliza un sistema de tipo simple pero potente para expresar código de nivel medio y garantizar la seguridad. Esto permite una compilación eficiente hasta CASM.

---

## Motivación
En Cairo 0, los desarrolladores escribían contratos de Starknet en Cairo, los compilaban en CASM y desplegaban el resultado de la compilación directamente en Starknet. Los usuarios podían entonces interactuar con los contratos de Starknet llamando a funciones de contratos inteligentes, firmando una transacción y enviándola al secuenciador. El secuenciador ejecutaría la transacción para obtener la tarifa de transacción del usuario, el prover (SHARP) generaría la prueba para el lote que incluyera esta transacción, y el secuenciador cobraría una tarifa por incluir la transacción en un bloque.

<div align="center" >
<img src="https://github.com/Starknet-Es/StarknetEs-Aprendizaje/blob/master/assets/transacciones.png" width="700" >
</div>

Sin embargo, este flujo Cairo 0 induce algunos problemas:

* Sólo las afirmaciones válidas pueden probarse en Cairo, por lo que las transacciones fallidas no pueden probarse. Es imposible probar una sentencia inválida como `assert 0 = 1`, ya que se traduce en restricciones polinómicas que no pueden ser satisfechas.
* La ejecución de una transacción puede fallar, resultando en que la transacción no se incluya en un bloque. En este caso, el secuenciador hace trabajo libre. Dado que las transacciones fallidas no tienen pruebas válidas, no pueden ser incluidas, y no hay forma de hacer cumplir la transferencia de tasas para el secuenciador.
* El secuenciador puede ser objeto de ataques DDoS con transacciones no válidas, haciendo que trabaje gratis y no cobre ninguna tasa por ejecutar estas transacciones.
* Es imposible distinguir entre censura (cuando un secuenciador decide a propósito no incluir ciertas transacciones) y transacciones inválidas, ya que ambos tipos de transacciones no se incluirán en los bloques.

En Ethereum, todas las transacciones fallidas se marcan como revertidas, pero se siguen incluyendo en los bloques, lo que permite a los validadores cobrar comisiones por transacción fallida. Para evitar que los usuarios malintencionados bombardeen la red con transacciones no válidas y abrumen a los secuenciadores, deteniendo la red al no poder procesarse las transacciones legítimas, Starknet necesita un sistema similar que permita a los secuenciadores cobrar tasas por las transacciones fallidas. Para resolver estos problemas, la red Starknet debe alcanzar dos objetivos: integridad y solidez. La integridad garantiza que siempre se pueda probar la ejecución de una transacción, incluso cuando se espera que falle. La solidez garantiza que las transacciones válidas no sean rechazadas, evitando la censura.

Sierra es correcto por construcción, lo que permite al secuenciador cobrar una tarifa por todas las transacciones. En lugar de desplegar código que puede fallar (por ejemplo, aserciones), podemos desplegar código de bifurcación (por ejemplo, if/else). Las aserciones de Cairo 1 se traducen a código ramificado Sierra, permitiendo que los errores se propaguen de vuelta al punto de entrada original que devuelve un valor booleano de verdadero/falso para el éxito o fracaso de la transacción. El SO Starknet puede determinar si una transacción es válida basándose en el valor de retorno del punto de entrada, y decidir si aplicar o no la actualización de estado si la transacción ha tenido éxito.

Cairo 1 ofrece una sintaxis similar a Rust y abstrae las construcciones de seguridad de Sierra para crear un lenguaje de programación demostrable y fácil de desarrollar. Compila a Sierra, una representación intermedia correcta por construcción de código Cairo que no contiene ninguna semántica de fallo. Esto asegura que ningún código Sierra pueda fallar, y se compila después a un subconjunto seguro de CASM. Los desarrolladores pueden centrarse en escribir contratos inteligentes eficientes sin preocuparse de escribir código que no falle, todo ello con primitivas de seguridad mejoradas.

En lugar de desplegar código CASM en Starknet, los desarrolladores compilarán su código Cairo 1 en Sierra y desplegarán programas Sierra en Starknet. En una declaración-transacción, los secuenciadores serán responsables de compilar el código Sierra a CASM, asegurando que ningún código que falle pueda ser desplegado en Starknet.

---

## Correcto por construcción
Para diseñar un lenguaje que no pueda fallar, primero debemos identificar las operaciones inseguras en Cairo 0. Estas incluyen:

* Referencias a direcciones de memoria ilegales; intentar acceder a una celda de memoria no asignada.
* Aserciones, ya que pueden fallar sin posibilidad de recuperación.
* Múltiples escrituras a la misma dirección de memoria debido al modelo de memoria write-once de Cairo
* Bucles infinitos que hacen imposible determinar si un programa finalizará.

## Garantizar que las desreferencias no puedan fallar
En Cairo 0, los desarrolladores podían escribir el siguiente código que intentaba acceder al contenido de una celda de memoria no asignada.

```cairo
let (ptr:felt*) = alloc();
tempvar x = [ptr];
```

El sistema de tipos de Sierra previene los errores comunes relacionados con los punteros aplicando reglas estrictas de propiedad y haciendo uso de punteros inteligentes como `Box`, permitiendo así la detección y prevención de desreferencias de punteros inválidos en tiempo de compilación. El tipo `Box<T>` sirve como puntero a una instancia de puntero válida e inicializada y proporciona dos funciones para instanciar y desreferenciar: `box_new<T>()` y `box_deref()`. Al utilizar el sistema de tipos para detectar errores de desreferenciación en tiempo de compilación, CASM compilado desde Sierra evita la desreferenciación de punteros inválidos.

---

## Garantizar que ninguna celda de memoria se escribe dos veces
En El Cairo 0, los usuarios usaban matrices como ésta:

```cairo
let (array:felt*) = alloc();
assert array[0] = 1;
assert array[1] = 2;
assert array[1] = 3; // fails
```

Sin embargo, intentar escribir dos veces en el mismo índice de array resulta en un error de ejecución, ya que las celdas de memoria sólo pueden escribirse una vez. Para evitar este problema, Sierra introduce un tipo `Array<T>` junto con una función `array_append<T>(Array<T>, value:T) -> Array<T>`. Esta función toma una instancia de un array y un valor a añadir y devuelve la instancia actualizada del array apuntando a la siguiente celda de memoria libre. Como resultado, los valores se añaden secuencialmente al final de las matrices sin preocuparse por posibles conflictos debidos a celdas de memoria ya escritas.

Para asegurar que las instancias de arreglos previamente usadas que ya han sido agregadas no sean reutilizadas, Sierra usa un sistema de tipos lineal que asegura que los objetos se usen exactamente una vez. Como tal, cualquier instancia de arreglo que ya haya sido anexada no puede ser reutilizada en otra llamada a `array_append`.

El código de abajo muestra un fragmento de un programa Sierra que crea un felt array y agrega el valor `1` dos veces usando la libfunc `array_append`. En el código, la primera llamada `array_append` toma la variable array con id `[0]` como entrada y devuelve una variable con id `[2]` que representa el array actualizado. Esta variable se utiliza como parámetro de entrada para la siguiente llamada `array_append`. Es importante notar que la variable con id `[0]` no es reutilizable una vez consumida por la libfunc, e intentar invocar `array_append` con `[0]` como parámetro de entrada resultará en un error de compilación.

```cairo
array_new<felt>() -> ([0]);
felt_const<1>() -> ([1]);
store_temp<felt>([1]) -> ([1]);
array_append<felt>([0], [1]) -> ([2]);
felt_const<1>() -> ([4]);
store_temp<felt>([4]) -> ([4]);
array_append<felt>([2], [4]) -> ([5]);
```

Para objetos que pueden ser reutilizados múltiples veces, como los `felts`, Sierra provee la función `dup<T>(T) -> (T,T)`, que devuelve dos instancias del mismo objeto que pueden ser usadas para diferentes operaciones. Esta función sólo se implementa para tipos que son seguros de duplicar, típicamente tipos que no contienen arrays o diccionarios.

---

## Aserciones que no fallan
Las aserciones suelen emplearse para evaluar el resultado de una expresión booleana en un punto concreto del código. Si la evaluación no es la prevista, se produce un error. Al contrario que en Cairo 0, la compilación de una instrucción de `assert` o `aserción` de Cairo 1 producirá código de ramificación Sierra. Este código terminará la ejecución de la función actual prematuramente si la aserción no se satisface y continuará con las siguientes instrucciones en caso contrario.

---

## Garantizar la solidez de los programas que utilizan diccionarios
Los diccionarios sufren el mismo problema de append múltiple que los Arrays, que puede resolverse introduciendo un tipo especial `Dict<K,V>` y un conjunto de funciones de utilidad para instanciar, recuperar y establecer valores de diccionario. Sin embargo, existe un **problema de solidez con los diccionarios. Cada diccionario debe ser aplastado (squashed) al final de un programa llamando a la función `dict_squash(Dict<K,V>) -> ()`, que verifica la consistencia de la secuencia de actualizaciones de las claves. Los diccionarios no aplastados son peligrosos, ya que un prover malicioso podría probar la corrección de actualizaciones inconsistentes.

Como vimos anteriormente, el sistema de tipos lineal obliga a que los objetos se utilicen exactamente una vez. La única forma de "usar" un Dict es llamar a la función `dict_squash`, que usa la instancia del diccionario y no devuelve nada. Esto significa que los diccionarios no aplastados serán detectados al compilar el código Sierra a CASM, y se lanzará un error en tiempo de compilación. Para otros tipos que no necesariamente necesitan ser usados exactamente una vez, usualmente tipos que no contienen Dicts, Sierra introduce la función `drop<T>(T)->()` que usa una instancia de un objeto y no devuelve nada.

Vale la pena notar que tanto `drop` como `dup` no producen ningún código CASM. Simplemente proveen seguridad de tipo a nivel de Sierra, asegurando que las variables se usen exactamente una vez.

---

## Prevención de bucles infinitos
Determinar si un programa finalmente se detendrá o se ejecutará para siempre es un problema fundamental de la informática conocido como el Problema de Detención (Halting Problem) y, por desgracia, no tiene solución en el caso general. En un entorno distribuido como Starknet, donde los usuarios pueden desplegar y ejecutar código arbitrario, es importante evitar que los usuarios ejecuten bucles infinitos como el siguiente código de Cairo.

```cairo
fn foo() {
	foo()
}
```

Como las funciones recursivas pueden ser una fuente de bucles infinitos si nunca se cumple la condición de parada, el compilador de Cairo a Sierra inyectará un método `withdraw_gas` al principio de las funciones recursivas. Como esta característica aún no se ha implementado, se espera que los desarrolladores llamen a `withdraw_ga` en funciones recursivas y manejen el resultado ellos mismos, aunque debería incluirse en una futura versión del compilador.

Esta función `withdraw_gas` deducirá la cantidad de gas necesaria para ejecutar la función del gas total disponible para la transacción evaluando el coste de ejecutar cada instrucción de la función. El coste se analiza determinando cuántos pasos lleva cada operación, lo que se conoce en tiempo de compilación para la mayoría de las operaciones. Durante la ejecución de un programa Cairo, si la llamada a `withdraw_gas` devuelve un valor nulo o negativo, la ejecución de la función en curso se detendrá, todas las variables pendientes se consumirán llamando a `dict_squash` sobre los diccionarios no aplastados y llamando a `drop` sobre otras variables, y la ejecución se considerará fallida. Dado que todas las transacciones en Starknet tienen una cantidad limitada de gas gastable para ejecutar las transacciones, se evitan los bucles infinitos - y asegurándose de que todavía hay suficiente gas disponible para soltar las variables y detener la ejecución, el secuenciador podrá cobrar las tasas del fallo de la transacción.

---

## CASM seguro gracias a un conjunto restringido de instrucciones
El objetivo principal de Sierra es garantizar que el código CASM generado no pueda fallar. Para lograrlo, los programas Sierra se componen de instrucciones que invocan `libfuncs`. Estas son un conjunto de funciones de biblioteca incorporadas para las cuales se garantiza que el código CASM generado es seguro. Por ejemplo, la libfunc `array_append` genera código CASM seguro para agregar un valor a un arreglo.

Este enfoque para lograr la seguridad del código permitiendo sólo un conjunto de funciones de biblioteca seguras y fiables es similar a la filosofía del lenguaje de programación Rust. Al proporcionar a los desarrolladores un conjunto de abstracciones seguras y de confianza, ambos lenguajes ayudan a prevenir errores comunes de programación y aumentan la seguridad y fiabilidad del código. Cairo 1 utiliza un sistema de propiedad y préstamo similar al de Rust, proporcionando a los desarrolladores una forma de razonar sobre la seguridad del código en tiempo de compilación, lo que puede ayudar a prevenir errores y mejorar la calidad general del código.

---

## RESUMEN
Sierra sirve como intermediario crucial entre los lenguajes de programación de alto nivel de Cairo y los objetivos de compilación más primitivos como CASM, garantizando que el CASM generado es seguro para ejecutarse en Starknet. Su diseño se centra en la seguridad, utilizando un conjunto restringido de funciones que producen código CASM seguro, un compilador robusto combinado con un sistema de tipos lineal para evitar errores en tiempo de ejecución, y un sistema de gas incorporado para evitar bucles infinitos.

En la siguiente parte, nos enfocaremos en entender la anatomía de un programa Sierra, proporcionando los requerimientos básicos necesarios para leer y entender programas Sierra.

---

## Descargo de responsabilidad
Este artículo ha sido preparado para la información y comprensión general de los lectores, y no indica el respaldo de Nethermind a ningún activo, proyecto o equipo en particular, ni garantiza su seguridad. Ninguna representación o garantía, expresa o implícita, es dada por Nethermind en cuanto a la exactitud o integridad de la información u opiniones contenidas en el presente artículo. Ningún tercero debe confiar en este artículo de ninguna manera, incluyendo, sin limitación, como asesoramiento financiero, de inversión, fiscal, regulatorio, legal o de otro tipo, o interpretar este artículo como cualquier forma de recomendación.

Tenga en cuenta que, si bien Nethermind presta servicios a Starkware, este artículo no se ha elaborado como parte de dichos servicios.

---

## Acerca de Nethermind
Nethermind es un equipo de constructores e investigadores de talla mundial. Ayudamos a empresas y desarrolladores de todo el mundo a acceder y construir sobre la web descentralizada. Nuestro trabajo toca cada parte del ecosistema web3, desde nuestro nodo Nethermind hasta la investigación criptográfica fundamental y la infraestructura para el ecosistema Starknet. Descubra nuestro conjunto de herramientas para Starknet: [Warp, el compilador de Solidity a Cairo;](https://nethermind.page.link/8a9f) [Voyager, un explorador de bloques de StarkNet;](https://nethermind.page.link/htwC) [Horus, la herramienta de verificación formal de código abierto](https://nethermind.io/horus/) para los contratos inteligentes de Starknet; [Juno, la implementación del cliente de Starknet,](https://github.com/NethermindEth/juno) y los servicios de [Auditoría de Seguridad de los Contratos Inteligentes de Cairo.](https://nethermind.page.link/25ee)

Si estás interesado en resolver algunos de los problemas más difíciles de blockchain, ¡visita nuestra [bolsa de trabajo](https://nethermind.page.link/careers)!
