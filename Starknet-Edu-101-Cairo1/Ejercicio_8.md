## Ejercicio 8 - Recursiones Nivel 1

- [Contrato Cairo ex08](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex08.cairo)
- [Starkscan ex08](https://testnet.starkscan.co/contract/0x01ec8e981b1b6a7256a71f21790dd07cafeb15d02c18534a2bd4a6c8551860aa)
- [Voyager ex08](https://goerli.voyager.online/contract/0x01ec8e981b1b6a7256a71f21790dd07cafeb15d02c18534a2bd4a6c8551860aa)

----

### Función para resolver

Revise `fn claim_points()`

```cairo
    #[external]
    fn claim_points() {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();
        let user_value_at_slot_ten = user_values::read((sender_address, 10_u128));
        assert(user_value_at_slot_ten == 10_u128, 'USER_VALUE_NOT_10');

        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }
```

---

## Solución

En este caso `set_user_values(account, values)` debe tener el valor 10.

1. El slot inicia en 0, el max es len del array. Empieza a escribir los valores del array de izquierda a derecha en `set_user_values`. En este caso no importa los valores que ingrese en las posiciones 0-9 contando desde la izquierda, lo unico que nos interesa es que en la posicion 10 contando de la izquierda tenga el valor 10.

Así que solo deberas de pasar la `account` y el array `[10, 9, 8, 7, 6, 5, 4, 3, 2, 1, 0] ` y podrás conseguir los puntos.

<img src="/imágenes/8.0.png" alt="Imagen 1" width="75%">

<div align="center">

[Hash Claim ex08](https://testnet.starkscan.co/tx/0x1c9298820cf53caa2e711601b3faaaa57c65057330df78d38be69e49b81d2fa)
