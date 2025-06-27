# Registro de actividades (logs)

📚 **Logging en Soroban: Cómo registrar información útil durante la ejecución de contratos inteligentes**

El **logging** es una herramienta esencial para los desarrolladores de contratos inteligentes, ya que permite registrar información clave durante la ejecución del contrato. Estos registros pueden ser útiles para depuración, seguimiento del comportamiento del contrato o para proveer información de auditoría.

⚠️ En Soroban, los logs no almacenan datos permanentes, pero pueden verse en el historial de ejecución. Son eventos _diagnósticos_ y no afectan el estado del contrato.

### Código en Soroban

Nos vamos a una ruta donde queramos crear nuestro proyecto y ponemos el siguiente comando:

```bash
stellar contract init  logging --name logging
```

Borramos el contrato generado en  lib.rs y ponemos el siguiente código

#### 📌 Código del contrato: `lib.rs`

```rust
rustCopiarEditar#![no_std]
use soroban_sdk::{contract, contractimpl, log, Env, Symbol};

#[contract]
pub struct Contract;

#[contractimpl]
impl Contract {
    pub fn hello(env: Env, value: Symbol) {
        log!(&env, "Hello {}", value);
    }
}

mod test;
```

**🛠 Explicación de la función `hello`**

```rust
rustCopiarEditarpub fn hello(env: Env, value: Symbol) {
    log!(&env, "Hello {}", value);
}
```

🔍 **Descripción:**

La función `hello` utiliza el macro `log!` de Soroban para registrar un mensaje que incluye un valor dinámico.

📌 **Mecanismo:**

* `log!(&env, "Hello {}", value);` → Utiliza el macro `log!`, que acepta el entorno (`env`) y una cadena formateada estilo Rust.
* `{}` es un marcador de posición que se reemplaza por el valor pasado, en este caso, un `Symbol`.
* Este log no modifica el estado del contrato y es accesible mediante las herramientas de análisis de Soroban.

🧠 **Importante:** Los logs son visibles cuando se ejecutan pruebas o cuando se hace seguimiento en una red (por ejemplo, testnet) con herramientas como el CLI de Soroban.

Borramos el contrato generado en  test.rs y ponemos el siguiente código

🧪 Código de prueba: `test.rs`

```rust
#![cfg(test)]

use super::*;
use soroban_sdk::{
    symbol_short, testutils::Logs, xdr::Hash, xdr::ScAddress, Address, BytesN, Env, TryFromVal,
};

extern crate std;

#[test]
fn test() {
    let env = Env::default();

    let id_bytes = BytesN::from_array(&env, &[8; 32]);

    let addr: Address =
        Address::try_from_val(&env, &ScAddress::Contract(Hash(id_bytes.to_array()))).unwrap();
    let contract_id = env.register_at(&addr, Contract, ());
    let client = ContractClient::new(&env, &contract_id);

    client.hello(&symbol_short!("Dev"));

    let logs = env.logs().all();
    assert_eq!(logs, std::vec!["[Diagnostic Event] contract:CAEAQCAIBAEAQCAIBAEAQCAIBAEAQCAIBAEAQCAIBAEAQCAIBAEAQMCJ, topics:[log], data:[\"Hello {}\", Dev]"]);
    std::println!("{}", logs.join("\n"));
}
```

**🧪 ¿Qué hace esta prueba?**

1️⃣ **Configura un entorno (`env`) local de pruebas.**\
2️⃣ **Crea una dirección y registra el contrato.**\
3️⃣ **Llama a la función `hello` pasando el símbolo `"Dev"`.**\
4️⃣ **Verifica que se haya registrado correctamente el log esperado.**

#### 📝 Resultado esperado del log:

```
bashCopiarEditar[Diagnostic Event] contract:<ID_CONTRATO>, topics:[log], data:["Hello {}", Dev]
```

📌 Este log muestra:

* El ID del contrato que generó el log.
* El tipo de evento: `log`.
* Los datos logueados, en este caso `"Hello {}"` y el símbolo `Dev`.

***

#### 🧠 Cuándo usar `log!`

✅ Para seguimiento de ejecución en diferentes entornos (testnet, futurenet).\
✅ Para depurar comportamientos inesperados.\
✅ Para mantener trazabilidad durante el desarrollo.\
❌ No usar para almacenar información crítica del negocio, ya que los logs no son persistentes ni recuperables dentro del contrato.

#### Compilación y prueba

```bash
contract build
cargo test -- --nocapture
```

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption><p>resultado de la ejecución</p></figcaption></figure>

#### 🧪 ¿Por qué se necesita `-- --nocapture`?

Por defecto, cuando ejecutas `cargo test`, Rust captura la salida estándar (`stdout`) y la salida de error (`stderr`) de cada prueba para mantener la salida de la terminal limpia y enfocada en los resultados de las pruebas. Esto significa que cualquier salida generada por macros como `println!` o `log!` no se mostrará en la terminal, a menos que una prueba falle.

El flag `--nocapture` le indica al ejecutable de pruebas que no capture estas salidas, permitiendo que cualquier mensaje impreso se muestre directamente en la terminal, independientemente de si la prueba pasa o falla.

