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

