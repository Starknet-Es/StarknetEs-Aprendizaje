## Ejercicio 9 - Recursiones Nivel 2

- [Contrato Cairo ex09](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex09.cairo)
- [Starkscan ex09](https://testnet.starkscan.co/contract/0x053b96c4ee027c53ea001479f24c10b543063e3c26d037c600e5bd31f0b21e5c)
- [Voyager ex09](https://goerli.voyager.online/contract/0x053b96c4ee027c53ea001479f24c10b543063e3c26d037c600e5bd31f0b21e5c)

----

### Función para resolver

Revise `fn claim_points()`

```cairo
#[external]
    fn claim_points(array: Array::<u128>) {
        assert(!array.is_empty(), 'EMPTY_ARRAY');
        assert(array.len() >= 4_u32, 'ARRAY_LEN_LT_4');

        // Calculating the sum of the array sent by the user
        let mut sum: u128 = 0_u128;
        let total = get_sum_internal(sum, array);
        assert(total >= 50_u128, 'SUM_LT_50');

        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();

        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }

```

---

## Solución

Hay que pasar un array de 4 elementos donde la suma de estos 4 sea igual a 50, pero mientras se hace la suma se tiene que cumplir una condición. Como el ejercicio anterior empieza a iterar de izquierda a derecha. La condición que se tiene que cumplir es que la suma temporal * 2 tiene que ser menor o igual que la suma temporal + el elemento actual.

Para hacer esto, se debe utilizar una función recursiva. Una función recursiva es una función que se llama a sí misma. En este caso, la función recursiva sería la encargada de recorrer los elementos del array y hacer la suma. Cada vez que se llama a la función recursiva, se suma el elemento actual al valor temporal, y se comprueba si se cumple la condición.

Si la condición se cumple, se llama a la función recursiva con el siguiente elemento del array y la suma temporal actualizada. Si no se cumple, se devuelve un valor de error. Si se llega al final del array y se ha cumplido la condición, se devuelve el valor de la suma total.

En este caso para el 50 haremos lo siguiente:

```bash
Elemento | Current_Suma  |  Sum = Current_Suma + Elemento  |
1        | 0             |  1                              |
4        | 1             |  5                              |
12       | 5             |  17                             |
33       | 17            |  50                             |
```

1. Así que solo deberás de pasar la Array de 4 elementos que cumplen estas condiciones `33,12,4,1` para reclamar los puntos.

<img src="/imágenes/9.0.png" alt="Imagen 1" width="75%">

<div align="center">

[Hash Claim ex09](https://testnet.starkscan.co/tx/0x62405d8fe82c19b07b1586a64deb4feeb3cd4a516dac24dec3d86485c8dfab4)
