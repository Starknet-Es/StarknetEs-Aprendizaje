## Ejericio 3 - Leyendo y escribiendo variables de almacenamiento

- [Contrato Cairo ex03](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex03.cairo)
- [Starkscan ex03](https://testnet.starkscan.co/contract/0x033d5fc40c0e262612528a9a652ada70be854d98241fb7548745262b5273c9d1)
- [Voyager ex03](https://goerli.voyager.online/contract/0x033d5fc40c0e262612528a9a652ada70be854d98241fb7548745262b5273c9d1)

---

### Función para resolver

Revise `fn claim_points()`

 ```cairo
    #[external]
    fn claim_points() {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();
        // Checking that user's counter is equal to 3 (the value we want to reach) and throwing an error if it is not
        let current_counter_value = user_counters::read(sender_address);
        // We are using the Cairo assert function to throw an error if the condition is not met
        assert(current_counter_value == 3_u128, 'Counter is not equal to 3');

        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }
 ```

---

## Solución

En este ejercicio debemos modificar la variable user_counters_storage con las funciones `increment_counter()`(suma 2) y `decrement_counter()`(resta 1) para que sea igual a `3` y poder obtener los puntos.

El contador en la variable `user_counters_storage` tiene el valor de 0.

1. Llamamos a la función 2 veces para que `user_counters_storage()` tenga el valor de 4.

<img src="/imágenes/3.0.png" alt="Imagen 1" width="75%">
<img src="/imágenes/3.1.png" alt="Imagen 2" width="75%">

2. Llamamos a la función 1 vez para que `user_counters_storage()` tenga el valor de 3.

<img src="/imágenes/3.2.png" alt="Imagen 3" width="75%">
<img src="/imágenes/3.3.png" alt="Imagen 4" width="75%">

3. De esta manera ya podemos obtener los puntos.

<img src="/imágenes/3.4.png" alt="Imagen 4" width="75%">

<div align="center">

[Incrementar 2](https://testnet.starkscan.co/tx/0x368124a5866643a51911428a59006be1929b1d6d33bc143356adc9614eded4e) - [Incrementar 2](https://testnet.starkscan.co/tx/0x6543f48110dbeea7086a22a6a0307c76005b15fd7bcd6017ae39e6ad58ab802) - [Decrementar 1](https://testnet.starkscan.co/tx/0x372da067942d0478733808b73a008fc5525666c6cc12d1687e392f895dfa0a5) - [Hash ex03]( https://testnet.starkscan.co/tx/0x2f8105bccdf6b3cf2c9af91c902f559e37aaa13be087a76009621a58dd9fdb9w)

