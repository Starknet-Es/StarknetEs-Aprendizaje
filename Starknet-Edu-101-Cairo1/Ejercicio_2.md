## Ejericio 2 - Variables de almacenamiento, getters, asserts

- [Contrato Cairo ex02](https://github.com/starknet-edu/starknet-cairo-101/blob/main/src/ex02.cairo)
- [Starkscan ex02](https://testnet.starkscan.co/contract/0x0600f8fe0752e598b4e6b27839f00ad65215d129f385e12931323c487b6f9b36)
- [Voyager ex02](https://goerli.voyager.online/contract/0x0600f8fe0752e598b4e6b27839f00ad65215d129f385e12931323c487b6f9b36)

En este ejercicio debemos averiguar el valor de `my_secret_value_storage` para poder obtener los puntos.

---

### Función para resolver

Revise `fn claim_points()`

 ```cairo
   #[external]
    fn claim_points(my_value: u128) {
        // Reading caller address using the Starknet core library function get_caller_address() (similar to msg.sender in Solidity)
        // and storing it in a variable called sender_address.
        let sender_address = get_caller_address();
        // Reading the secret value from storage using the read function from the storage variable my_secret_value_storage
        let my_secret_value = my_secret_value_storage::read();
        // Checking that the value sent is the same as the secret value stored in storage using the assert function
        // Using assert this way is similar to using "require" in Solidity
        assert(my_value == my_secret_value, 'Wrong secret value');
        // Checking if the user has validated the exercise before sending points using the validate_exercise function from the Ex00Base contract
        validate_exercise(sender_address);
        // Sending points to the address specified as parameter using the distribute_points function from the Ex00Base contract
        distribute_points(sender_address, 2_u128);
    }
 ```

---

## Solución

1. Llamamos a la función `my_secret_value()` y guardamos el valor para añadir en el claim.
<img src="/imágenes/2.0.png" alt="Imagen 1" width="75%">

2. Ingresamos el valor obtenido en la función `claim points`. De esta manera ya podemos obtener los puntos.
<img src="/imágenes/2.1.png" alt="Imagen 2" width="75%">

<div align="center">

[Hash ex02](https://testnet.starkscan.co/tx/0x3086f693d4c221ac491a8440143623b30bd58f9b0810335ca91f2b7590997)
