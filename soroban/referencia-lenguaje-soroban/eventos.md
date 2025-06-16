# Eventos

### 📚¿Qué son los Eventos?

Los eventos en Soroban son como **notificaciones** que tu contrato inteligente envía al mundo exterior. Imagínate que tu contrato es como una caja negra 📦, y los eventos son como notas que saca por una ranura para contarte qué está pasando adentro.

#### 🎯 ¿Para qué sirven?

* **Transparencia**: Permiten que aplicaciones externas sepan qué ha ocurrido
* **Debugging**: Te ayudan a rastrear qué hace tu contrato
* **Integración**: Otras apps pueden "escuchar" estos eventos y reaccionar
* **Auditoría**: Crean un registro inmutable de las acciones

#### 🔍 Tipos de Eventos

1. **System Events** 🔧: Generados automáticamente por Soroban
2. **Contract Events** 📝: Los que tú defines en tu contrato

### Código en Soroban

Nos vamos a una ruta donde queramos crear nuestro proyecto y ponemos el siguiente comando:

```bash
stellar contract init  events --name events
```

Borramos el contrato generado en  lib.rs y ponemos el siguiente código

#### 📌 Código del contrato: `lib.rs`

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, contracttype, symbol_short, Env, Symbol};
// 📝 Definimos los tipos de datos para nuestros eventos
#[contracttype]
#[derive(Clone, Debug, Eq, PartialEq)]
pub struct CounterCreated {
    pub initial_value: u32,
    pub creator: Symbol,
}
#[contracttype]
#[derive(Clone, Debug, Eq, PartialEq)]
pub struct CounterUpdated {
    pub old_value: u32,
    pub new_value: u32,
    pub operation: Symbol,
}
#[contracttype]
#[derive(Clone, Debug, Eq, PartialEq)]
pub struct CounterReset {
    pub previous_value: u32,
    pub reset_by: Symbol,
}
// 🏗️ Definimos las claves para almacenar datos
const COUNTER: Symbol = symbol_short!("COUNTER");
#[contract]
pub struct CounterContract;
#[contractimpl]
impl CounterContract {
    /// 🎯 Inicializa el contador y emite evento de creación
    pub fn initialize(env: Env, initial_value: u32, creator: Symbol) {
        // Guardamos el valor inicial
        env.storage().instance().set(&COUNTER, &initial_value);

        // 📢 ¡Emitimos nuestro primer evento!
       
         env.events().publish(
            (symbol_short!("counter"), symbol_short!("created")), // Topics para filtrar
            CounterCreated {
                initial_value,
                creator,
            },
        );
    }

    /// ➕ Incrementa el contador
    pub fn increment(env: Env) -> u32 {
        let current: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);

        let new_value = current + 1;
        env.storage().instance().set(&COUNTER, &new_value);

        // 📢 Evento de actualización
        env.events().publish(
            (symbol_short!("counter"), symbol_short!("updated")),
            CounterUpdated {
                old_value: current,
                new_value,
                operation: symbol_short!("increment"),
            },
        );

        new_value
    }
    /// ➖ Decrementa el contador
    pub fn decrement(env: Env) -> u32 {
        let current: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);

        let new_value = if current > 0 { current - 1 } else { 0 };
        env.storage().instance().set(&COUNTER, &new_value);

        // 📢 Evento de actualización
        env.events().publish(
            (symbol_short!("counter"), symbol_short!("updated")),
            CounterUpdated {
                old_value: current,
                new_value,
                operation: symbol_short!("decrement"),
            },
        );

        new_value
    }
    /// 🔄 Resetea el contador a cero
    pub fn reset(env: Env, reset_by: Symbol) {
        let current: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);

        env.storage().instance().set(&COUNTER, &0u32);

        // 📢 Evento de reset
        env.events().publish(
            (symbol_short!("counter"), symbol_short!("reset")),
            CounterReset {
                previous_value: current,
                reset_by,
            },
        );
    }
    /// 👀 Obtiene el valor actual (sin eventos, es solo lectura)
    pub fn get_counter(env: Env) -> u32 {
        env.storage().instance().get(&COUNTER).unwrap_or(0)
    }
    /// 📊 Función que emite evento personalizado con datos múltiples
    pub fn log_milestone(env: Env, milestone: u32, message: Symbol) {
        let current: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);

        // 📢 Evento complejo con múltiples datos
        env.events().publish(
            (symbol_short!("counter"), symbol_short!("milestone")),
            (current, milestone, message),
        );
    }
}
```

### 🎯 Explicación Detallada de los Conceptos

#### 📢 ¿Cómo Funcionan los Eventos?

Los eventos en Soroban funcionan como un **sistema de notificaciones**:

1. **Topics** 🏷️: Son como "etiquetas" que permiten filtrar eventos
   * Primer topic: Identifica el contrato
   * Segundo topic: Identifica el tipo de evento
2. **Data** 📦: La información específica del evento
   * Puede ser un struct, tupla, o valor simple
   * Se serializa automáticamente

#### 🔧 Componentes Clave

```rust
rustenv.events().publish(    (topic1, topic2),  // 🏷️ Topics para filtrar    event_data         // 📦 Datos del evento);
```

#### 🛠 Explicación de la función `initialize`

```rust
rustCopiarEditarpub fn initialize(env: Env, initial_value: u32, creator: Symbol) {
    env.storage().instance().set(&COUNTER, &initial_value);

    env.events().publish(
        (symbol_short!("counter"), symbol_short!("created")),
        CounterCreated {
            initial_value,
            creator,
        },
    );
}
```

🔍 **Descripción**:\
Inicializa el valor del contador y emite un evento indicando su creación.

📌 **Mecanismo**:

* `env.storage().instance().set(...)` → Guarda el valor inicial del contador en el almacenamiento persistente del contrato.
* `env.events().publish(...)` → Publica un evento del tipo `CounterCreated`, que incluye el valor inicial y el símbolo del creador.
* `(symbol_short!("counter"), symbol_short!("created"))` → Define los _topics_ del evento, útiles para filtrarlo desde herramientas de análisis.

🧠 **Importante**:\
Este evento puede ser consultado luego en herramientas como el explorador de eventos de Soroban para auditar quién creó el contador y con qué valor.

***

#### 🛠 Explicación de la función `increment`

```rust
rustCopiarEditarpub fn increment(env: Env) -> u32 {
    let current: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);
    let new_value = current + 1;
    env.storage().instance().set(&COUNTER, &new_value);

    env.events().publish(
        (symbol_short!("counter"), symbol_short!("updated")),
        CounterUpdated {
            old_value: current,
            new_value,
            operation: symbol_short!("increment"),
        },
    );

    new_value
}
```

🔍 **Descripción**:\
Incrementa el valor del contador en 1 y emite un evento con los detalles del cambio.

📌 **Mecanismo**:

* Obtiene el valor actual del contador, lo incrementa, lo guarda nuevamente y emite un evento `CounterUpdated` con:
  * `old_value`: valor antes del incremento.
  * `new_value`: valor luego del incremento.
  * `operation`: símbolo `"increment"`.

🧠 **Importante**:\
Este evento permite rastrear cada operación que afectó el estado del contador.

***

#### 🛠 Explicación de la función `decrement`

```rust
rustCopiarEditarpub fn decrement(env: Env) -> u32 {
    let current: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);
    let new_value = if current > 0 { current - 1 } else { 0 };
    env.storage().instance().set(&COUNTER, &new_value);

    env.events().publish(
        (symbol_short!("counter"), symbol_short!("updated")),
        CounterUpdated {
            old_value: current,
            new_value,
            operation: symbol_short!("decrement"),
        },
    );

    new_value
}
```

🔍 **Descripción**:\
Disminuye el valor del contador (si es mayor a cero) y registra el cambio con un evento.

📌 **Mecanismo**:

* Se protege contra valores negativos con una verificación.
* El evento publicado incluye la operación `"decrement"`.

🧠 **Importante**:\
El estado nunca bajará de 0, manteniéndose válido en todo momento.

***

#### 🛠 Explicación de la función `reset`

```rust
rustCopiarEditarpub fn reset(env: Env, reset_by: Symbol) {
    let current: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);
    env.storage().instance().set(&COUNTER, &0u32);

    env.events().publish(
        (symbol_short!("counter"), symbol_short!("reset")),
        CounterReset {
            previous_value: current,
            reset_by,
        },
    );
}
```

🔍 **Descripción**:\
Establece el contador en cero y emite un evento indicando quién lo reseteó.

📌 **Mecanismo**:

* Guarda el valor anterior para registrarlo en el evento `CounterReset`.
* `reset_by` permite rastrear al responsable del reinicio.

🧠 **Importante**:\
Este evento es útil para auditoría o lógica de negocio que responda a resets.

***

#### 🛠 Explicación de la función `get_counter`

```rust
rustCopiarEditarpub fn get_counter(env: Env) -> u32 {
    env.storage().instance().get(&COUNTER).unwrap_or(0)
}
```

🔍 **Descripción**:\
Consulta el valor actual del contador sin modificar el estado del contrato.

📌 **Mecanismo**:

* Solo lectura: no genera logs ni eventos.
* Devuelve 0 si el contador nunca fue inicializado.

🧠 **Importante**:\
Función ideal para mostrar el estado actual en una interfaz de usuario o al cliente.

***

#### 🛠 Explicación de la función `log_milestone`

```rust
rustCopiarEditarpub fn log_milestone(env: Env, milestone: u32, message: Symbol) {
    let current: u32 = env.storage().instance().get(&COUNTER).unwrap_or(0);

    env.events().publish(
        (symbol_short!("counter"), symbol_short!("milestone")),
        (current, milestone, message),
    );
}
```

🔍 **Descripción**:\
Permite emitir un evento especial para marcar hitos del contador.

📌 **Mecanismo**:

* El evento contiene una tupla con el valor actual, el hito alcanzado, y un mensaje descriptivo.
* No altera el estado del contrato.

🧠 **Importante**:\
Útil para casos de uso como alcanzar un récord, pasar un umbral o anunciar logros.

**Compilación del contrarto:**\


```bash
stellar contract build
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Resultado de la compilación</p></figcaption></figure>

**Despliegue del contrato**&#x20;

**Mac/Linux**

```bash
stellar contract deploy \
--wasm target\wasm32v1-none\release\events.wasm \
--source developer \
--network testnet \
--alias events
```

**Windows**

```bash
stellar contract deploy `
--wasm target\wasm32v1-none\release\events.wasm `
--source developer `
--network testnet `
--alias events
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Contrato desplegado</p></figcaption></figure>

**Pruebas de ejecución y visualización de los eventos**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

\
**Empezando en valores iniciales el contrato**\
stellar contract invoke \*\
\--id \<CONTRACT\_ID> \*\
\--source \*\
\--network testnet \*\
\-- \*\
initialize \*\
\--initial\_value 42 \*\
\--creator "developer"\


<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Resultado de la ejecución</p></figcaption></figure>

**Podemos ver algo nuevo en el resultado**

```json
 Event: [{"symbol":"counter"},{"symbol":"created"}] = {"map":[{"key":{"symbol":"creator"},"val":{"symbol":"developer"}},{"key":{"symbol":"initial_value"},"val":{"u32":42}}]}
```

Se ve lo generado por el siguiente código de la función **initialize**

```rust
// 📢 ¡Emitimos nuestro primer evento!
       
         env.events().publish(
            (symbol_short!("counter"), symbol_short!("created")), // Topics para filtrar
            CounterCreated {
                initial_value,
                creator,
            },
        );
```

Si nos vamos al explorador y miramos [la última transacción](https://stellar.expert/explorer/testnet/contract/CDWGT4HLBJRN6ETWRC4KPYQ4OPUAJB4EZZHGR5O6NXD2OCGOHA2RNZDX)

podemos ver lo siguiente:

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>Visualización en el explorador</p></figcaption></figure>

Vemos que función se llamó y con que parámetros

También observamos evento en la parte de "raised event"  con el contenido que le hemos pasado.



**Otra opción de ver el evento por el cliente de Stellar**

**1** por[ lumenscan](https://testnet.lumenscan.io/) sacamos el ledger donde está la operación:, esto lo obtenemos por el historial de operacione del contrato y con que billetera interactuó.  En esta caso la billetera "developer"

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption><p>ledger <a href="https://testnet.lumenscan.io/ledgers/1420216">1420216</a></p></figcaption></figure>

**2** en la consola ejecutamos:\


```bash
stellar events --network testnet --start-ledger 1420216
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>Resultados de la operación</p></figcaption></figure>

Si vemos la parte Value vemos ambos valores del evento el 42 y el valor developer.
