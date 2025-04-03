---
description: Clásico hello world autogenerado por el cliente de Stellar
---

# helloworld

Nos vamos a una ruta donde queramos crear nuestro proyecto y ponemos el siguiente comando:

```
stellar contract init helloworld
```

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption><p>Estructuras de archivos generados</p></figcaption></figure>

Como podemos observar el proyecto como tal se llama _helloworld_ y en la carpeta contracts se creó una carpeta llamada hello-world, este contrato se genera por defecto cuando creamos un proyecto pero no indicamos como se llama el contrato como tal

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

**Análisis del código generado**

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption><p>archivo lib.rs dentro de la carpeta hello-world</p></figcaption></figure>

1 **Declaración de No-Standard Library**

`#![no_std]`

* Esta directiva le indica al compilador que el contrato se compilará sin la biblioteca estándar de Rust. Esto con el fin de hacer lo más liviano posible.

2 **Importación de Elementos del SDK**

_use soroban\_sdk::{contract, contractimpl, vec, Env, String, Vec};_

* _contract_\
  Es un macro atributo que se aplica a una estructura para marcarla como el contenedor del contrato. Esto permite que el entorno de Soroban reconozca el contrato y genere la metadata necesaria.
* _contractimpl_\
  Es otro macro atributo que se utiliza en el bloque impl para indicar que las funciones definidas en ese bloque serán exportadas y podrán ser invocadas desde el entorno de ejecución del contrato.
* _vec_\
  Es un macro propio de Soroban que crea un vector (colección\
  dinámica) adaptado al entorno de contratos. Permite inicializar vectores de forma similar a vec! de Rust, pero considerando las particularidades del entorno.
* _Env_\
  Es una estructura que representa el entorno en el que se ejecuta el contrato. A través de ella se accede a funciones esenciales como el almacenamiento, emisión de eventos, logs, etc.
* _String_\
  Es una implementación propia del tipo cadena optimizada para entornos sin estándar (no\_std) y adaptada a las limitaciones de ejecución de los contratos inteligentes en Soroban.
* _Vec_\
  Es el tipo vector (colección dinámica) proporcionado por Soroban, similar al Vec estándar de Rust pero diseñado para trabajar de forma segura y eficiente en contratos inteligentes.

**Definición del Contrato**

```
//#[contract]
pub struct Contract;
```

**Implementación de las Funciones del Contrato**

```
#[contractimpl]
impl Contract {
    pub fn hello(env: Env, to: String) -> Vec<String> {
        vec![&env, String::from_str(&env, "Hello"), to]
    }
}
```

* _**La anotación #\[contractimpl]**_\
  sobre el bloque impl Contract indica que las funciones definidas serán expuestas para que puedan ser llamadas por el entorno de Soroban.
* _**Función hello:**_

Firma:

```
pub fn hello(env: Env, to: String) -> Vec
```

Recibe dos argumentos:\
&#xNAN;_**env: Env**_: El entorno de ejecución, que se usa para acceder a funcionalidades propias del runtime de Soroban.

**to: String**: Una cadena que se pasa como parámetro.\
Retorna un Vec, es decir, un vector de cadenas.

**Cuerpo de la función:**

```
vec![&env, String::from_str(&env, "Hello"), to]
```

Se usa el macro vec! propio de Soroban para crear un vector.\
**Elementos del vector:**

1. _\&env:_ Se pasa el entorno para que el macro pueda asignar memoria o gestionar la colección en el contexto de Soroban.
2. _String::from\_str(\&env, "Hello"):_ Convierte el literal "Hello" en un objeto de tipo String propio de Soroban, usando el entorno env para la conversión.
3. _to:_ Se agrega la cadena que se recibió como argumento.
4. Al no tener ";" al final es el valor a retornar que se especifica en la función

_**Compilación:**_

```
stellar contract build
```

_**Despliegue:**_

* **Mac/Linux**

```
stellar contract deploy \
  --wasm target/wasm32-unknown-unknown/release/hello_world.wasm \
  --source developer \
  --network testnet \
  --alias hello_world
```

* **Windows**

```
stellar contract deploy `
  --wasm target/wasm32-unknown-unknown/release/hello_world.wasm `
  --source developer `
  --network testnet `
  --alias hello_world
```

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

**Pruebas del contrato**

**Linux y MAC**

```
stellar contract invoke \
--id CD5VLB73SUDKSXGWA5B3U52V4PUS7BKNILRN5CRNCWBDX4PAC44OBUVK \
--source developer \
--network testnet \
-- \
hello \
--to Pascual
```

**Windows**

```
stellar contract invoke `
--id CD5VLB73SUDKSXGWA5B3U52V4PUS7BKNILRN5CRNCWBDX4PAC44OBUVK `
--source developer `
--network testnet `
-- `
hello `
--to Manolo
```

**Obtenemos lo siguiente:**

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>
