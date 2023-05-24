## Ejercicio 10 - Componibilidad

- [Contrato Cairo ex10](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex10.cairo)
- [Starkscan ex10](https://testnet.starkscan.co/contract/0x0763e89551900eba82d757a9f3862935cc7f7e47538f01ddba514f23d9a5f6e0)
- [Voyager ex10](https://goerli.voyager.online/contract/0x0763e89551900eba82d757a9f3862935cc7f7e47538f01ddba514f23d9a5f6e0)

----

### Función para resolver

Revise `fn claim_points()`

```cairo
    #[external]
    fn claim_points(secret_value_i_guess: u128, next_secret_value_i_chose: u128) {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();

        // Retrieve secret value by READING
        let ex10b_addr = ex10b_address::read();

        let secret_value = Iex10bDispatcher{contract_address: ex10b_addr}.get_secret_value();
        assert(secret_value == secret_value_i_guess, 'NOT_EXPECTED_SECRET_VALUE');

        // choosing next secret_value for contract 10b. We don't want 0, it's not funny
        assert(next_secret_value_i_chose != 0_u128, 'SECRET_VALUE_IS_ZERO');

        Iex10bDispatcher{contract_address: ex10b_addr}.change_secret_value(next_secret_value_i_chose);

        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }

```

---

## Solución

En este ejercicio el valor secreto está en otro contrato. Vamos a llamarlo contrato B ex10b.cairo, pero en el contrato A es donde tenemos que ingresar el valor secreto.

1. Primero debemos encontrar el address del contrato B. Se obtiene del contrato A llamando a la función `get_ex10b_address()`.

<img src="/imágenes/10.0.png" alt="Imagen 1" width="75%">

2. Buscamos en el contrato B en [Starkscan](https://testnet.starkscan.co/contract/0x073d5ceb7e20bc50e555e326f60c25fe95bbb20f75892a3552eb77fcd14dae92#read-write-contract) y llamamos a la función `get_secret_value()` y obtenemos el valor secreto, es este caso `1547987`

<img src="/imágenes/10.1.png" alt="Imagen 2" width="75%">

3. Ahora iremos al contrato A. Ingresamos el valor secreto y nuevo valor en `claim_points`, en este caso fue `696969` de esta manera ya podemos obtener los puntos.

<img src="/imágenes/10.2.png" alt="Imagen 3" width="75%">

<div align="center">

[Hash Claim ex10](https://testnet.starkscan.co/tx/0x5ba86b8645e4c7787b0f1d79700a11fdb7a0200b6e7839ff787cd0876f7d71f)
