## Ejercicio 4 - Mappings 

- [Contrato Cairo ex04](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex04.cairo)
- [Starkscan ex04](https://testnet.starkscan.co/contract/0x06967cd33c6e064087123958e239c98f0de5e6d663660fa16a2526e8b115688a)
- [Voyager ex04](https://goerli.voyager.online/contract/0x06967cd33c6e064087123958e239c98f0de5e6d663660fa16a2526e8b115688a)

---

### Función para resolver

Revise `fn claim_points()`

```cairo
    #[external]
    fn claim_points(expected_value: u128) {
        // Reading caller address
        let sender_address: ContractAddress = get_caller_address();
        // Reading the slot assigned to the caller address in the mapping user_slots.
        // The value was assigned when assign_user_slot() was called by the user (see below) and is stored in the mapping user_slots
        let user_slot = user_slots::read(sender_address);
        // Checking that the user has a slot assigned to they (i.e. that he called assign_user_slot() before)
        assert(user_slot != 0_u128, 'ASSIGN_USER_SLOT_FIRST');

        // Checking that the value provided by the caller is the one we expect
        // Yes, I'm sneaky
        let value = values_mapped::read(user_slot);
        assert(value == expected_value + 32_u128, 'NOT_EXPECTED_SECRET_VALUE');

        // Checking if the user has validated the exercise before
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter
        distribute_points(sender_address, 2_u128);
    }
```

---

## Solución

En este ejercicio debemos hacer que el valor `values_mapped_storage` sea igual a `expected_value + 32`.

1. Primero iremos a `write smart` y nos asignamos un slot.

<img src="/imágenes/4.0.png" alt="Imagen 1" width="75%">

2. Copiamos el valor del slot y vamos a `read smart`.

<img src="/imágenes/4.1.png" alt="Imagen 2" width="75%">

3. Llamamos a la función `values_mapped()` y nos guardo el valor.

<img src="/imágenes/4.2.png" alt="Imagen 3" width="75%">

4. Ingresamos el valor obtenido en la función anterior `4950` pero le restamos 32, ya que dentro de la función `claim_points()` le suman este valor. De esta manera ya podemos obtener los puntos.

<img src="/imágenes/4.3.png" alt="Imagen 4" width="75%">


<div align="center">

[Asignar Slot](https://testnet.starkscan.co/tx/0x4ec2283eae9a61aad70105a65403f6b1035ee44b4677ed36383866e1b90217d) - [Hash Claim ex04](https://testnet.starkscan.co/tx/0x3fa44dc97d3ef8a10919fedf10f40ca872cad37d2183c958f7ef2c7eef39922)

