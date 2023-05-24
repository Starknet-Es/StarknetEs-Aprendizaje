## Ejercicio 5 - Visibilidad de variables

- [Contrato Cairo ex05](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex05.cairo)
- [Starkscan ex05](https://testnet.starkscan.co/contract/0x076c32e000f7112724bba3c5f51fb1290217a1010ae555e6ecbdb2bfe6613e33)
- [Voyager ex05](https://goerli.voyager.online/contract/0x076c32e000f7112724bba3c5f51fb1290217a1010ae555e6ecbdb2bfe6613e33)

----

### Función para resolver

Revise `fn claim_points()`

```cairo
#[external]
    fn claim_points(expected_value: u128) {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();
        // Reading user slot and verifying it's not zero
        let user_slot = user_slots::read(sender_address);
        assert(user_slot != 0_u128, 'ASSIGN_USER_SLOT_FIRST');

        // Checking that the value provided by the user is the one we expect
        // Yes, I'm sneaky
        let value = values_mapped_secret::read(user_slot);
        assert(value == expected_value + 32_u128, 'NOT_EXPECTED_SECRET_VALUE');

        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }
```

---

## Solución

En este ejercicio debemos hacer que el valor `values_mapped_secret_storage` sea igual a `expected_value + 23` .

1. Primero iremos a `write smart` y nos asignamos un slot.

<img src="/imágenes/5.0.png" alt="Imagen 1" width="75%">

2. Llamamos a la función `get_user_slots()` añadiendo nuestra wallet. Como verán es 0. 

<img src="/imágenes/5.1.png" alt="Imagen 2" width="75%">

3. Debemos llamar a la función `copy_secret_value_to_readable_mapping()` para que modifique el valor.

<img src="/imágenes/5.2.png" alt="Imagen 3" width="75%">

4. Llamamos a la función `get_users_values()` y guardamos el valor.

<img src="/imágenes/5.3.png" alt="Imagen 4" width="75%">

5. Ingresamos el valor obtenido en la función anterior pero haremos los siguiente, si te ha tocado un slot par o impar se te asignará un resultado u otro, en nuestro caso nos ha servido este resume `Slot Par`

#### Slot Par
En un slot par tipo 28 y con un valor ejemplo `9775` deberemos:

```bash
9775 - 32 + 23 = 9766
```
<img src="/imágenes/5.4.png" alt="Imagen 5" width="75%">

#### Slot Impar
En un slot impar tipo 29 y con un valor ejemplo `1481` deberemos:

```bash
1481 + 32 -23 = 1472
```

<div align="center">

[Asigne Slot](https://testnet.starkscan.co/tx/0x2c3b5b4189540e145dd8a1cf594b3a8354c9bce0628a9f9907bc29be853e587) - [Copy Secret Value To Readable Mapping](https://testnet.starkscan.co/tx/0x15a300b1cc025e4d266a4ceba9eecf8acede583e26102ca21a3d57cef9cfbb2) - [Hash Claim ex05](https://testnet.starkscan.co/tx/0x7ca0903d627bb1af6393ac0c7236fc36435fe59c108b8585ec3421281d3ac85)
