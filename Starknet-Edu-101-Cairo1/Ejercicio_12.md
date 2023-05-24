## Ejercicio 12 - Eventos

- [Contrato Cairo ex12](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex12.cairo)
- [Starkscan ex12](https://testnet.starkscan.co/contract/0x04a221a8e3155fb03d1708881213a2ecdb05a41cf0ae6de83ddcf8f12bb04282)
- [Voyager ex12](https://goerli.voyager.online/contract/0x04a221a8e3155fb03d1708881213a2ecdb05a41cf0ae6de83ddcf8f12bb04282)

----

### Función para resolver

Revise `fn claim_points()`

```cairo
       #[external]
    fn claim_points(expected_value: u128) {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();
        // Checking that the user got a slot assigned
        let user_slot = user_slots::read(sender_address);
        assert(user_slot != 0_u128, 'ASSIGN_USER_SLOT_FIRST');

        // Checking that the value provided by the user is the one we expect
        // Still sneaky.
        // Or not. Is this psyops?
        let value = values_mapped_secret::read(user_slot);
        assert(value == expected_value, 'NOT_EXPECTED_SECRET_VALUE');

        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }
```


---

## Solución

Deberemos encontar el ultimo valor secreto y añadir un nuevo valor dentro de los rangos. 

1. Al llamar la función `assign_user_slot()` se emite un evento donde está el valor secreto. 

<img src="/imágenes/12.0.png" alt="Imagen 1" width="75%">

2. Buscamos en los eventos creados y revisamos el valor obtenido para `secret_value`, en este caso `8665`.

<img src="/imágenes/12.1.png" alt="Imagen 2" width="75%">

3. Para reclamar los puntos deberemos restarle `32` al valor secreto para que se distribuyan, en este caso `8633`

<img src="/imágenes/12.2.png" alt="Imagen 3" width="75%">

<div align="center">

[Asigne Slot](https://testnet.starkscan.co/tx/0x66a1c0bbb301485bf834603816397d211f6ddbd707f47dcb862ad2d650ba59b) - [Hash Claim ex12](https://testnet.starkscan.co/tx/0x7eda66b8f3d981ac14eaac46f15c065d14e54c07824f92b3bc999a3bc8cf4bf)
