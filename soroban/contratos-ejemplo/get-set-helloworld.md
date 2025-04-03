---
description: Contrato típico donde podemos poner y extraer un mensaje
---

# get set helloworld

Nos vamos a una ruta donde queramos crear nuestro proyecto y ponemos el siguiente comando:

```
stellar contract init get_set_helloworld --name get_set_message
```

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption><p>Borramos el contrato generado por defecto y lo sustituimos por el siguiente código</p></figcaption></figure>

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, Env, Symbol, symbol_short, String};

const MESSAGE: Symbol = symbol_short!("Message");

#[contract]
pub struct MessageContract;

#[contractimpl]
impl MessageContract {

    pub fn set_message(env: Env, message: String) {
                env.storage().instance().set(&MESSAGE, &message)
    }

    pub fn get_message(env: Env) -> String {
        env.storage().instance().get(&MESSAGE)
            .unwrap_or(String::from_str(&env, "Default Message"))
    }

}
```

### **Análisis del código:**

**Importaciones del SDK de Soroban**

```rust
use soroban_sdk::{contract, contractimpl, Env, Symbol, symbol_short, String};
```

* _**Symbol y symbol\_short:**_\
  Se usan para trabajar con identificadores cortos y\
  optimizados en el entorno de Soroban.
  * _Symbol_ Es un string de 32 caracteres, viene ocupa 64 bit (a-z A-Z 0-9)
  * _symbol\_short!_ Es un string de 9 caracteres (a-z A-Z 0- 9)
* _**String:**_\
  \
  Es una versión adaptada del tipo cadena de texto para el entorno sin estándar (no\_std) y específicamente optimizada para contratos inteligentes en Soroban.

**Definición de una Constante**\
`const MESSAGE: Symbol = symbol_short!("Message");`

### 🛠 **Explicación de las funciones**

Todas las funciones trabajan con `env: Env`, que proporciona acceso al almacenamiento y otras funcionalidades del entorno Soroban.

#### 1️⃣ `set_message(env: Env, message: String)`

✅ **Guarda un mensaje en el almacenamiento.**

📌 **Paso a paso:**

1. Toma como entrada un **mensaje de tipo `String`**.
2. Usa `env.storage().instance().set()` para almacenar el mensaje en la blockchain usando la clave `MESSAGE`.

***

#### 2️⃣ `get_message(env: Env) -> String`

✅ **Obtiene el mensaje almacenado.**

📌 **Paso a paso:**

1. Intenta recuperar el valor asociado a `MESSAGE` en el almacenamiento.
2. Si la clave no existe, usa `.unwrap_or()` para devolver un mensaje por defecto: `"Default Message"`.
3. Devuelve el mensaje almacenado o el mensaje por defecto.

***

### 📌 **Resumen**

Este contrato inteligente permite almacenar y recuperar un mensaje de la blockchain de Soroban:

* `set_message()`: Guarda un mensaje en la blockchain.
* `get_message()`: Obtiene el mensaje guardado o un mensaje por defecto si aún no se ha establecido.

**Compilación del contrato**\
la compilación y creación del webassembly es con el siguiente comando:\
`stellar contract build`

**Despliegue del contratro**

Para Mac y Linux el salto de línea es con el caractér " **\\**" y en Windows con el caracter " **´** "

Reemplaze el simbolo \* por el respectivo caractér de salto de linea a su sistema operativo.

```
stellar contract deploy *
  --wasm target/wasm32-unknown-unknown/release/get_set_message.wasm *
  --source developer *
  --network testnet *
  --alias get_set_message
```

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

**Pruebas del contratro**

Para **linux y Mac** el salto de línea de la instrucción es con el caracter " \ " para **Windows** con el caracter " \` "



**Invocación de la función set\_message**

```
stellar contract invoke `
  --id CCSZAYLAUZ6FR656ORQMQJOOY3X6Y4BLRHDI3DW4YYWCIYUYWFOYQLB4 `
  --source developer `
  --network testnet `
  -- `
  set_message `
  --message "Hey master developer 😉"
```

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

**Invocación de la función get\_message**

```
stellar contract invoke `
  --id CCSZAYLAUZ6FR656ORQMQJOOY3X6Y4BLRHDI3DW4YYWCIYUYWFOYQLB4 `
  --source developer `
  --network testnet `
  -- `
  get_message 
```

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>
