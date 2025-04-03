# 2️ Segundos pasos



Una vez visto lo básico del cliente, miremos lo pertinente enfocado a los contratos inteligentes, como es:

* Creación de un contratos.
* Compilación de contratos.
* Despliegue.
* Ejecución de contrato.

**Creación de un contrato**\
Syntaxis:\
`stellar contract init "nombre_contrato"`

`stellar contract init hola_mundo`

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

**Estructura y archivos creados**

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

Dentro de lib.rs está el contrato por defecto

```
#![no_std]
use soroban_sdk::{contract, contractimpl, vec, Env, String, Vec};

#[contract]
pub struct Contract;

#[contractimpl]
impl Contract {
    pub fn hello(env: Env, to: String) -> Vec<String> {
        vec![&env, String::from_str(&env, "Hello"), to]
    }
}

mod test;
```

**Compilación del contrato**\
Se ejecuta el siguiente comando:\




```
stellar contract build
```

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption><p>Estructura de archivos generada bajo la carpeta release</p></figcaption></figure>

Podemos ver que se creo una carpeta llamada release, más internamente vemos que está creado el archivo hello\_world.wasm

💡el nombre del archivos en web assembly es el que automáticamente se pone dentro del archivo Cargo.toml

**Despliegue del contrato**

\
**Mac/linux**

```
stellar contract deploy \
--wasm /<RUTA_DE_LA_CARPETA>/target/wasm32-unknown-unknown/release/hello_world.wasm \
--source developer \
--network testnet \
--alias hello_world
```

**Windows**

```
stellar contract deploy `
--wasm /<RUTA_DE_LA_CARPETA>/target/wasm32-unknown-unknown/release/hello_world.wasm `
--source developer `
--network testnet `
--alias hello_world
```



Felicidades, ya tienes el contratro deplegado en testnet 🥳

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://stellar.expert/explorer/testnet/contract/CDCIWIAOWCDIORMZWV53VY5DVLOGB5PWG4ETSAEKKHX43RZVSPYUSLRS?filter=history" %}
Link del contrato desplegado en la red testnet de Stellar
{% endembed %}

**Interactuando con los contratos**\
**Sintaxis**\
`stellar contract invoke`\
`--id "contrato"`\
`--source "identidad"`\
`--network testnet`\
`--`\
`"función"`\
`--"parametro" "dato del parámetro"`

**Linux y MAC**

```
stellar contract invoke \
--id CDCIWIAOWCDIORMZWV53VY5DVLOGB5PWG4ETSAEKKHX43RZVSPYUSLRS \
--source developer \
--network testnet \
-- \
hello \
--to Pascual
```

**Windows**

```
stellar contract invoke `
--id CDCIWIAOWCDIORMZWV53VY5DVLOGB5PWG4ETSAEKKHX43RZVSPYUSLRS `
--source developer `
--network testnet `
-- `
hello `
--to Manolo
```



**El resultado es:**

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>
