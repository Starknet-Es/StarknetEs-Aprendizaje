## Ejercicio 13 - Privacidad en Starknet

- [Contrato Cairo ex13](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex13.cairo)
- [Starkscan ex13](https://testnet.starkscan.co/contract/0x067ed1d23c5cc3a34fb86edd4f8415250c79a374e87bcf2e6870321261ca9b0f)
- [Voyager ex13](https://goerli.voyager.online/contract/0x067ed1d23c5cc3a34fb86edd4f8415250c79a374e87bcf2e6870321261ca9b0f)

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

Deberemos encontar el valor secreto  que se nos asigna a nuestro slot y añadirlo para resolver el ejercicio. 

1. Primero llamamos `assign_user_slot()`.

<img src="/imágenes/13.0.png" alt="Imagen 1" width="75%">

2. Luego buscamos en `get_user_slots` que ranura es la que se nos ha asignado, en este caso el `7`.

<img src="/imágenes/13.1.png" alt="Imagen 2" width="75%">

3. Luego iremos a la transacción de la creación del contrato [ex13](https://testnet.starkscan.co/contract/0x067ed1d23c5cc3a34fb86edd4f8415250c79a374e87bcf2e6870321261ca9b0f#account-calls) y verificamos en la posición 7 el valor `4987` y copiamos el valor de la siguiente ranura de la `8` que sería `2249`.

<img src="/imágenes/13.2.png" alt="Imagen 3" width="75%">

<div align="center">

[Asigne Slot](https://testnet.starkscan.co/tx/0x3c7dadec3d1bebe733ba49a55adfb20068b42073e154253d5f16fc5717c484d) - [Hash Claim ex13](https://testnet.starkscan.co/tx/0x60fc36a8fdc6430855c82c26df21e7d35ec679cccc46bd92ebc35ad02e007b5)
