## Ejercicio 7 - Comparando valores

- [Contrato Cairo ex07](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex07.cairo)
- [Starkscan ex07](https://testnet.starkscan.co/contract/0x006051096480f375894eebb99948bce14a84c25093636c4b4e8222cc32a67cf0)
- [Voyager ex07](https://goerli.voyager.online/contract/0x006051096480f375894eebb99948bce14a84c25093636c4b4e8222cc32a67cf0)

----

### Función para resolver

Revise `fn claim_points()`

```cairo
    #[external]
    fn claim_points(value_a: u128, value_b: u128) {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();
        // Checking that value_a and value_b fit the desired properties
        assert(value_a != 0_u128, 'ZERO_VALUE');
        assert(value_b >= 0_u128, 'LESS_THAN_ZERO_VALUE');
        assert(value_a != value_b, 'EQUAL_VALUE');
        assert(value_a <= 75_u128, 'GREATER_VALUE');
        assert(value_a >= 40_u128 & value_a <= 70_u128, 'NOT_IN_BETWEEN_VALUE');
        assert(value_b < 1_u128, 'LESS_THAN_VALUE');

        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }
```

---

## Solución

Se deben ingresar 2 valores A y B que cumplan las siguientes condiciones:

```bash
* A ≠ 0
* B ≥ 0
* A ≠ B
* A ≤ 75
* 40 ≤ A ≤ 70
* B < 1
```

1. Primero iremos a `write smart` y añadimos dentro de esos valores por ejemplo `A = 50` y `B = 0`

<img src="/imágenes/7.0.png" alt="Imagen 1" width="75%">


<div align="center">

[Hash Claim ex07](https://testnet.starkscan.co/tx/0x6493f50f6635f2309e53e91118a11c00cd0af1059eeaa2da1a3e416759a15de)
