---
description: >-
  A pesar de ser un tipo Enum, dado su uso lo vamos a tratar  aparte para una
  mejor claridad
---

# Option

En **Rust**, el tipo **Option** es una forma segura y explícita de representar que un valor puede estar presente o no. Es como decir "puede haber algo o no" 🔍. En lugar de usar _null_ (que puede causar errores difíciles de encontrar), Rust usa **Option** para obligarte a manejar ambos casos.

La definición de **Option** es muy sencilla:

* **Some(valor):** Indica que sí hay un valor. 😊
* **None:** Indica que no hay ningún valor. 😞

#### Ejemplo

Imagina que queremos dividir dos números, pero tenemos que evitar dividir por cero. Podemos usar **Option** para devolver un resultado solo cuando la división tiene sentido:

```rust
fn dividir(a: f64, b: f64) -> Option<f64> {
    if b == 0.0 {
        // Si b es 0, no podemos dividir, así que devolvemos None 😞
        None
    } else {
        // Si b no es 0, devolvemos el resultado dentro de Some 😊
        Some(a / b)
    }
}

fn main() {
    let resultado = dividir(10.0, 2.0);
    
    // Usamos pattern matching para ver si hay un resultado
    match resultado {
        Some(valor) => println!("El resultado es: {}", valor), // Caso cuando hay valor 😊
        None => println!("No se puede dividir por cero 😞"),       // Caso cuando no hay valor
    }
}
```

**Contratos inteligentes ejemplo:**

Abrimos la consola en la ruta donde deseamos crear el proyecto y ejecutamos.

```bash
stellar contract init option --name option
```

&#x20;Borramos todo el código y ponemos lo siguiente:

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, symbol_short, Env, String, Symbol};

#[contract]
pub struct OptionContract;
#[contractimpl]
impl OptionContract {
    // Establece (o elimina) un mensaje en el almacenamiento del contrato.
    // Si se pasa Some(mensaje), se guarda. Si se pasa None, se borra.
    pub fn set_message(env: Env, message: Option<String>) {
        const key: Symbol = symbol_short!("msg");

        match message {
            Some(msg) => {
                // Guardamos el mensaje en el storage
                env.storage().instance().set(&key, &msg)
            }
            None => {
                // Si se pasa None, eliminamos el mensaje del storage
                env.storage().instance().remove(&key)
            }
        }
    }
    // Recupera el mensaje almacenado (si existe) del storage.
    // Devuelve Some(mensaje) si hay un mensaje, o None si no hay ninguno.
    pub fn get_message(env: Env) -> Option<String> {
        let key: Symbol = symbol_short!("msg");
        env.storage().instance().get(&key)
    }

    // Método de saludo que usa un parámetro opcional.
    // Si se proporciona un nombre, saluda a esa persona; si no, saluda a "amigo".
    pub fn greet(env: Env, name: Option<String>) -> String {
        match name {
            Some(n) => n,
            None => String::from_str(&env, "¡Hola, amigo! 😃"),
        }
    }
}
```

## Explicación del contrato

\
📌 **Estructuras y Tipos del Contrato**



1. **Atributo `#![no_std]`**\
   Indica que el contrato no utiliza la biblioteca estándar de Rust, lo cual es común en entornos embebidos o en contratos inteligentes para reducir dependencias y adaptarse a restricciones de recursos.
2. **Macros de Contrato**\
   `#[contract]` y `#[contractimpl]`\
   Estas macros son proporcionadas por el _soroban\_sdk_ y se usan para marcar:

* **`#[contract]`**: Define la estructura principal del contrato.
* **`#[contractimpl]`**: Implementa la lógica de negocio del contrato.\
  Para más detalles, consulta la documentación de [contratos Soroban](https://soroban.stellar.org/docs/developers/writing-contracts).

***

🛠 **Funciones del Contrato**

1️⃣ **set\_message**

```rust
rustCopiarEditarpub fn set_message(env: Env, message: Option<String>) {
    const key: Symbol = symbol_short!("msg");

    match message {
        Some(msg) => {
            // Guardamos el mensaje en el storage
            env.storage().instance().set(&key, &msg)
        }
        None => {
            // Si se pasa None, eliminamos el mensaje del storage
            env.storage().instance().remove(&key)
        }
    }
}
```

**Descripción:**\
Establece (o elimina) un mensaje en el almacenamiento del contrato. Si se pasa `Some(mensaje)`, se guarda el mensaje; si se pasa `None`, se elimina el mensaje existente en el storage.

**Mecanismo:**

* Se define una clave (key) con el símbolo `"msg"`.
* Se utiliza la estructura de control `match` para evaluar el parámetro `message`:
  * **Caso `Some(msg)`**: Se guarda el mensaje en el storage.
  * **Caso `None`**: Se elimina el mensaje del storage.

***

2️⃣ **get\_message**

```rust
rustCopiarEditarpub fn get_message(env: Env) -> Option<String> {
    let key: Symbol = symbol_short!("msg");
    env.storage().instance().get(&key)
}
```

**Descripción:**\
Recupera el mensaje almacenado en el storage del contrato. Devuelve `Some(mensaje)` si existe un mensaje, o `None` si no se ha establecido ninguno.

**Mecanismo:**

* Se define la misma clave `"msg"` con un `Symbol`.
* Se accede al storage del contrato para obtener el mensaje asociado a esa clave.

***

3️⃣ **greet**

```rust
rustCopiarEditarpub fn greet(env: Env, name: Option<String>) -> String {
    match name {
        Some(n) => n,
        None => String::from_str(&env, "¡Hola, amigo! 😃"),
    }
}
```

**Descripción:**\
Genera un saludo utilizando un parámetro opcional. Si se proporciona un nombre (`Some(nombre)`), se saluda a esa persona; si no se proporciona ningún nombre (`None`), se saluda por defecto a "amigo".

**Mecanismo:**

* Se utiliza `match` para evaluar el parámetro `name`:
  * **Caso `Some(n)`**: Devuelve el nombre proporcionado como saludo.
  * **Caso `None`**: Devuelve el saludo predeterminado `"¡Hola, amigo! 😃"` mediante `String::from_str`.

***

📌 **Resumen General**\
Este contrato inteligente demuestra cómo utilizar el tipo `Option` para gestionar valores que pueden estar presentes o no. Permite:

* **Establecer o eliminar datos en el storage** mediante la función `set_message`.
* **Recuperar datos del storage** con `get_message`, retornando un valor opcional.
* **Generar respuestas predeterminadas** en caso de ausencia de valor mediante la función `greet`.

**Compilación del contrato**

Ejecutamos lo siguiente:

```bash
Stellar contract build
```

**Despliegue del contrato**

Para Mac y Linux el salto de línea es con el carácter " **\\**" y en Windows con el carácter " **´** "

Reemplaze el simbolo \* por el respectivo carácter de salto de linea a su sistema operativo.

```bash
stellar contract deploy *
  --wasm target/wasm32-unknown-unknown/release/option.wasm *
  --source <Identity> *
  --network testnet *
  --alias option
```

<figure><img src="../../.gitbook/assets/image (60).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Pruebas del contrato**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

**Función set\_message:**

```bash
stellar contract invoke *                                                                          
--id <ID_CONTRACT> *
--source developer *
--network testnet *
-- *
set_message *
--message "Hola mundo 🙂"
```

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función get\_message:**

```bash
stellar contract invoke *                                                                          
--id <ID_CONTRACT> *
--source developer *
--network testnet *
-- *
get_message
```

<figure><img src="../../.gitbook/assets/image (62).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función get\_message:**

```bash
stellar contract invoke *                                                                          
--id <ID_CONTRACT> *
--source developer *
--network testnet *
-- *
greet
```

_Vamos a hacer que se ejecute el código por la opción none_

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption><p>Prueba de ejecución</p></figcaption></figure>
