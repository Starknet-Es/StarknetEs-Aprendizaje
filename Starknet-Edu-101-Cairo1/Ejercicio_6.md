## Ejercicio 6 - Visibilidad de variables

- [Contrato Cairo ex06](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex06.cairo)
- [Starkscan ex06](https://testnet.starkscan.co/contract/0x060987aea322cd12657588b6cdb0892db79322ab4533f7d74838ff2e2614a015)
- [Voyager ex06](https://goerli.voyager.online/contract/0x060987aea322cd12657588b6cdb0892db79322ab4533f7d74838ff2e2614a015)

----

### Función para resolver

Revise `fn claim_points()`

```cairo
#[external]
    fn claim_points(expected_value: u128) {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();
        // Reading user slot
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

Debemos averiguar el valor secreto en `values_mapped_secret_storage`.

1. Primero iremos a `write smart` y nos asignamos un slot, en nuestro caso `5`

<img src="/imágenes/6.0.png" alt="Imagen 1" width="75%">
<img src="/imágenes/6.1.png" alt="Imagen 2" width="75%">

2. Debemos llamar a función `external_handler_for_internal_function()` para que modifique el valor de `values_mapped_secret_storage`

<img src="/imágenes/6.2.png" alt="Imagen 3" width="75%">

3. Llamamos a la función `get_user_values(account)` y guardamos el valor, en nuestro caso `6802`

<img src="/imágenes/6.3.png" alt="Imagen 4" width="75%">

4. Llamamos a la función `claim_points` y añadimos el valor anterior.

<img src="/imágenes/6.4.png" alt="Imagen 5" width="75%">


<div align="center">

[Asigne Slot](https://testnet.starkscan.co/tx/0x59393692555fa9a92ba63db568812a7dd3935d3856b16ea658258116bcfa9e2) - [External Handler](https://testnet.starkscan.co/tx/0x4e409d1f0ba5c6d56e454656b0b6b11729c64dac2f60b76db4e74b96a2deda6) - [Hash Claim ex06](https://testnet.starkscan.co/tx/0xd1a05ad6baac7d3bc86c1c72297599144f7747be8e84a04cc9216ee1a1cc51)
