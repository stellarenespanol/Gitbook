---
description: Ejemplo de un contador
---

# counter

Para crear el contrato ejecutamos  lo siguiente:

```
stellar contract init  counter --name counter
```

Dentro de la carpeta counter, en el archivo lib.rs borramos todo el código y ponemos el siguiente código:

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, Env, Symbol, symbol_short, log};

const COUNTER: Symbol = symbol_short!("COUNTER");
#[contract]
pub struct CounterContract;

#[contractimpl]
impl CounterContract {
    
    pub fn add_to_counter(env: Env, increment: u32) {
        let mut count: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);
        log!(&env, "current count: {}", count);
        count += increment;
        env.storage().instance().set(&COUNTER, &count);
        env.storage().instance().extend_ttl(50, 100);
    }

    pub fn inc_counter(env: Env)  {
        let mut count: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);
        count += 1;
        env.storage().instance().set(&COUNTER, &count);
        env.storage().instance().extend_ttl(50, 100);
    }

    pub fn get_counter(env: Env) -> u32 {
        let count: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);
        count
    }
}

```

Básicamente es un contrato con un contador y 3 funciones:

* ```
  add_to_counter: Adiciona un numero al contador.
  ```
* {% code fullWidth="true" %}
  ```
  inc_counter: Incrementa en 1 el contador.
  ```
  {% endcode %}
*   ```
    get_counter: Obtiene el contador actual.
    ```



### **Análisis del código:**

**Importaciones del SDK de Soroban**

```rust
use soroban_sdk::{contract, contractimpl, Env, Symbol,symbol_short };
```

* _**Symbol y symbol\_short:**_\
  Se usan para trabajar con identificadores cortos y\
  optimizados en el entorno de Soroban.
  * _Symbol_ Es un string de 32 caracteres, viene ocupa 64 bit (a-z A-Z 0-9)
  * _symbol\_short!_ Es un string de 9 caracteres (a-z A-Z 0- 9)
* **log:** Muy parecido al log de javascript, se imprime por consola mensajes y contenido de variables

### 🛠 **Explicación de las funciones**

Todas las funciones trabajan con `env: Env`, que proporciona acceso al almacenamiento y otras funcionalidades del entorno Soroban.

#### 1️⃣ `add_to_counter(env: Env, increment: u32)`

✅ **Suma un número arbitrario al contador.**

📌 **Paso a paso:**

1. Recupera el valor actual de `COUNTER` desde el almacenamiento, usando `.unwrap_or(0)`, lo que garantiza que si la clave no existe, se toma como 0.
2. Suma el `increment` recibido como parámetro.
3. Guarda el nuevo valor en el almacenamiento.
4. Extiende el **Time-To-Live (TTL)** de la clave en el almacenamiento (de 50 a 100 bloques).

***

#### 2️⃣ `inc_counter(env: Env)`

✅ **Incrementa el contador en 1.**\
📌 **Paso a paso:**\
Esta función es similar a `add_to_counter`, pero en lugar de recibir un parámetro, simplemente suma `1` al contador.

***

#### 3️⃣ `get_counter(env: Env) -> u32`

✅ **Devuelve el valor actual del contador.**

📌 **Paso a paso:**

1. Recupera el valor de `COUNTER` del almacenamiento.
2. Si la clave no existe, devuelve `0`.
3. Retorna el valor del contador.

***

### 📌 **Resumen**

Este contrato mantiene un contador en la blockchain de Soroban con:

* `add_to_counter()`: Incrementa el contador en un valor específico.
* `inc_counter()`: Incrementa el contador en 1.
* `get_counter()`: Obtiene el valor actual del contador.

**Compilación del contrato:**

```
Stellar contract build
```

**Despliegue del contratro**

Para Mac y Linux el salto de línea es con el carácter " **\\**" y en Windows con el carácter " **´** "

Reemplaze el simbolo \* por el respectivo carácter de salto de linea a su sistema operativo.

```
stellar contract deploy *
  --wasm target/wasm32-unknown-unknown/release/counter.wasm *
  --source developer *
  --network testnet *
  --alias counter
  
  
```

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

**Pruebas del contratro**

Para **linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

**Invocación de la función add\_to\_counter**

```
stellar contract invoke *
  --id CARAAZHSZ5RYZDEPQ6GVNQKMQFQMAJRGP4I5KXSSW2NOGKA5EWAOTX7W *
  --source developer *
  --network testnet *
  -- *
    add_to_counter *
  --increment 200
```

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

**Invocación de la función inc\_counter**

```
stellar contract invoke *
  --id CARAAZHSZ5RYZDEPQ6GVNQKMQFQMAJRGP4I5KXSSW2NOGKA5EWAOTX7W *
  --source developer *
  --network testnet *
  -- *
    inc_counter
```

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

**Invocación de la función get\_counter**

```
stellar contract invoke *
  --id CARAAZHSZ5RYZDEPQ6GVNQKMQFQMAJRGP4I5KXSSW2NOGKA5EWAOTX7W *
  --source developer *
  --network testnet *
  -- *
    get_counter
```

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>
