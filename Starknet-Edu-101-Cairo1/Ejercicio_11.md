## Ejercicio 11 - Importando funciones

- [Contrato Cairo ex11](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex11.cairo)
- [Starkscan ex11](https://testnet.starkscan.co/contract/0x029a9a484d22a6353eff0d60ea56c6ffabaaac5e4889182287ef1d261578b197)
- [Voyager ex11](https://goerli.voyager.online/contract/0x029a9a484d22a6353eff0d60ea56c6ffabaaac5e4889182287ef1d261578b197)

----

### Función para resolver

Revise `fn claim_points()`

```cairo
    #[external]
    fn claim_points(secret_value_i_guess: u128, next_secret_value_i_chose: u128) {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();
        // Check if your answer is correct
        validate_answers(sender_address, secret_value_i_guess, next_secret_value_i_chose);
        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }

```

Revise ` fn secret_value_internal()`

```cairo
    fn secret_value_internal() -> u128 {
        let secret_value = ex11_secret_value::read();
        // There is a trick here
        if (secret_value > 340282366920938463463374607431768211455_u128 - 42069_u128)
        {return secret_value - 42069_u128;}
        else
        {return secret_value + 42069_u128;}
    }
```

---

## Solución

Deberemos encontar el ultimo valor secreto y añadir un nuevo valor dentro de los rangos. 

1. El secret value lo busca en `account call` y el ùltimo obtenido, en este caso era `9811398125`.

2. Buscamos en el contrato e indica que el nuevo valor debe de estar en un margen entre 42069 +/-, en este caso se ha pasado `9811398128` para distribuir los puntos.

<img src="/imágenes/11.0.png" alt="Imagen 1" width="75%">

[Hash Claim ex11](https://testnet.starkscan.co/tx/0x37b8ab5540360e7e99937c8d126bc05b007dd2cf0b8e98d043c92acbb5c4a0c)
