## Ejercicio 1 - Sintaxis general

- [Contrato Cairo ex01](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex01.cairo)
- [Starkscan ex01](https://testnet.starkscan.co/contract/0x031d1866cb827c4e27bbca9ffee59fa2158b679413ffb58c3f90af56e1140e85)
- [Voyager ex01](https://goerli.voyager.online/contract/0x031d1866cb827c4e27bbca9ffee59fa2158b679413ffb58c3f90af56e1140e85)


---

### Función para resolver

Revise `fn claim_points()`

```cairo
    #[external]
    fn claim_points() {
        // Reading caller address
        let sender_address = get_caller_address();
        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }
```

---

## Solución

En este ejercicio solo debemos entrar en el código del ejercicio, conectar nuestra wallet y en `write smart` llamar la función `claim_points()` para obtener los puntos.

<img src="/imágenes/1.0.png" alt="Imagen 1" width="75%">
<img src="/imágenes/1.1.png" alt="Imagen 2" width="75%">

<div align="center">

[Hash ex01](https://testnet.starkscan.co/tx/0x60da6fc7f6821b79abf62f0151b8e0361ac1de1cd2fcd3d011eed2584eaa3b5)
</div>
