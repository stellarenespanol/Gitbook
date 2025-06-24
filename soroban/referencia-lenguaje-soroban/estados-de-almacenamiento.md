# Estados de almacenamiento

## Tipos de Almacenamiento en Stellar Soroban

### 🌟 Introducción Teórica

Imagina que tu contrato inteligente es como una casa 🏠, y necesitas diferentes tipos de espacios para guardar tus cosas:

* 🗄️ Un archivo permanente para documentos importantes
* 📋 Una mesa de trabajo para proyectos actuales
* 🗑️ Una papelera para cosas temporales

En Soroban tenemos exactamente eso: **tres tipos de almacenamiento** que nos permiten gestionar nuestros datos de manera eficiente y económica.

***

### 🎯 Los Tres Tipos de Almacenamiento

#### 1. 💾 **PERSISTENT STORAGE** (Almacenamiento Persistente)

**🧠 ¿Qué es?**

Es el almacenamiento **más duradero y confiable** de Soroban. Los datos aquí se mantienen "para siempre" (hasta que explícitamente los borres). Es como guardar algo en una **caja fuerte** 🔒.

**✨ Características:**

* 🔄 **Permanente**: Los datos NO se borran automáticamente
* 💰 **Más costoso**: Requiere más fees porque ocupa espacio indefinidamente
* 🛡️ **Más seguro**: Ideal para datos críticos del contrato
* ⚡ **Acceso rápido**: Optimizado para lecturas frecuentes

**🎯 ¿Cuándo usarlo?**

* 👤 Información de usuarios (balances, perfiles)
* ⚙️ Configuraciones del contrato que no cambian
* 📊 Datos históricos importantes
* 🔑 Claves y identificadores únicos

**💡 Ejemplo Práctico:**

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, symbol_short, Address, Env};

#[contract]
pub struct TokenContract;

#[contractimpl]
impl TokenContract {
    pub fn set_balance(env: Env, user: Address, amount: i128) {
        let key = symbol_short!("balance");
        // Este dato debe persistir siempre 🔒
        env.storage().persistent().set(&(key, user), &amount);
    }
    
    pub fn get_balance(env: Env, user: Address) -> i128 {
        let key = symbol_short!("balance");
        env.storage().persistent().get(&(key, user)).unwrap_or(0)
    }
}
```



**Compilación del contrato:**\


```bash
stellar contract build
```

<figure><img src="../../.gitbook/assets/image (72).png" alt=""><figcaption><p>Resultado de la compilación</p></figcaption></figure>

**Despliegue del contrato**&#x20;

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "



```bash
stellar contract deploy *
--wasm target\wasm32v1-none\release\persistent.wasm *
--source developer *
--network testnet *
--alias persistent
```

<figure><img src="../../.gitbook/assets/image (73).png" alt=""><figcaption><p>Resultado del despliegue</p></figcaption></figure>

**Pruebas del contrato**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

#### Función set\_balance

```bash
stellar contract invoke *
--id <id contract> *
--source developer *
--network testnet *
-- *
set_balance *
--user  wallet_address*
--amount 1000000
```

<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption><p>Resultado del  llamado al contrato</p></figcaption></figure>

#### 2. 🏃‍♂️ **INSTANCE STORAGE** (Almacenamiento de Instancia)

**🧠 ¿Qué es?**

Es el almacenamiento **intermedio** de Soroban. Los datos aquí duran bastante tiempo, pero pueden "caducar" si no se usan. Es como tu **escritorio de trabajo** 📝 - mantienes las cosas que usas regularmente.

**✨ Características:**

* ⏳ **Semi-permanente**: Los datos pueden expirar si no se acceden
* 💸 **Costo medio**: Más barato que persistent, más caro que temporary
* 🔄 **Auto-renovable**: Cada acceso extiende su tiempo de vida
* 📈 **Eficiente**: Perfecto para datos que usas con frecuencia

**🎯 ¿Cuándo usarlo?**

* ⚙️ Configuraciones del contrato que pueden cambiar
* 📊 Metadatos del contrato (nombre, símbolo, decimales)
* 🎮 Estados de juego que duran varias partidas
* 📝 Información que se actualiza regularmente

💡 **Ejemplo Práctico:**

```rust
#[contractimpl]
impl TokenContract {
    // 🏃‍♂️ Configuración del token (INSTANCE)
    pub fn init_token(env: Env, name: String, symbol: String) {
        // Metadatos que se usan frecuentemente pero pueden cambiar
        env.storage().instance().set(&symbol_short!("name"), &name);
        env.storage().instance().set(&symbol_short!("symbol"), &symbol);
        env.storage().instance().set(&symbol_short!("decimals"), &7u32);
    }
    
    pub fn get_name(env: Env) -> String {
        env.storage().instance()
            .get(&symbol_short!("name"))
            .unwrap_or_else(|| String::from_str(&env, "Unknown Token"))
    }
    
    // 🔄 Actualizar configuración
    pub fn update_name(env: Env, new_name: String) {
        env.storage().instance().set(&symbol_short!("name"), &new_name);
        // ✨ Cada acceso renueva automáticamente el tiempo de vida
    }
}
```

**Compilación del contrato:**\


```bash
stellar contract build
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Resultado de la compilación</p></figcaption></figure>

**Despliegue del contrato**&#x20;

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

```bash
stellar contract deploy *
--wasm target\wasm32v1-none\release\instance.wasm *
--source developer *
--network testnet *
--alias instance
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Resultado del despliegue</p></figcaption></figure>

**Pruebas del contrato**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

#### Función init\_token

```bash
stellar contract invoke *
--id <id contract> *
--source developer *
--network testnet *
-- *
init_token *
--name  "Stellar Espanol"*
--symbol "SET"
```

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption><p>Resultado del  llamado al contrato</p></figcaption></figure>

#### 3. ⚡ **TEMPORARY STORAGE** (Almacenamiento Temporal)

**🧠 ¿Qué es?**

Es el almacenamiento **más barato y temporal** de Soroban. Los datos aquí se borran automáticamente después de un tiempo. Es como usar **notas adhesivas** 📝 para recordatorios rápidos.

**✨ Características:**

* ⏰ **Temporal**: Los datos se borran automáticamente (aprox. 24 horas)
* 💰 **Más barato**: Ideal para optimizar costos
* 🚀 **Rápido**: Perfecto para operaciones que no necesitan persistir
* 🧹 **Auto-limpieza**: No necesitas borrar manualmente

**🎯 ¿Cuándo usarlo?**

* 🔢 Cálculos intermedios en transacciones complejas
* 🎫 Nonces y tokens de sesión
* 📊 Caché de datos temporales
* 🔄 Estados de transacciones en progreso

**💡 Ejemplo Práctico:**

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, symbol_short, vec, Address, Env, String, Vec};

#[contract]
pub struct TokenContract;

#[contractimpl]
impl TokenContract {
    // ⚡ Operación de transferencia con estado temporal
    pub fn transfer_with_memo(env: Env, from: Address, to: Address, amount: i128, memo: String) {
        // 🔢 Guardamos temporalmente el ID de transacción
        let tx_id = env.ledger().sequence();
        let temp_key = symbol_short!("tx_temp");

        // ⚡ Datos temporales para esta transacción
        env.storage().temporary().set(
            &(temp_key, tx_id),
            &(from.clone(), to.clone(), amount, memo),
        );

        // Realizar la transferencia...
        // Los datos temporales se borrarán automáticamente ✨
    }

    // 🎮 Sistema de cooldown temporal
    pub fn use_special_ability(env: Env, user: Address) {
        let cooldown_key = symbol_short!("cooldown");

        // ⏰ Verificar si está en cooldown
        if env.storage().temporary().has(&(&cooldown_key, user.clone())) {
            panic!("🚫 Habilidad en cooldown!");
        }

        // ⚡ Activar cooldown temporal (se borra solo)
        env.storage().temporary().set(&(cooldown_key, user), &true);

        // Ejecutar habilidad especial...
    }
}
```

**Compilación del contrato:**\


```bash
stellar contract build
```

<figure><img src="../../.gitbook/assets/image (76).png" alt=""><figcaption><p>Resultado de la compilación</p></figcaption></figure>

### **Despliegue del contrato**&#x20;

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

```bash
stellar contract deploy *
--wasm target\wasm32v1-none\release\temporary.wasm *
--source developer *
--network testnet *
--alias temporary
```

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption><p>Resultado del despliegue</p></figcaption></figure>

### 🎯 Comparación Rápida

<table><thead><tr><th width="100.66668701171875">Aspecto</th><th>💾 Persistent</th><th>🏃‍♂️ Instance</th><th>⚡ Temporary</th></tr></thead><tbody><tr><td><strong>Duración</strong></td><td>♾️ Permanente</td><td>⏳ Semi-permanente</td><td>⏰ ~24 horas</td></tr><tr><td><strong>Costo</strong></td><td>💰💰💰 Alto</td><td>💰💰 Medio</td><td>💰 Bajo</td></tr><tr><td><strong>Uso ideal</strong></td><td>👤 Datos críticos</td><td>⚙️ Configuraciones</td><td>🔢 Cálculos temporales</td></tr><tr><td><strong>Auto-borrado</strong></td><td>❌ No</td><td>🔄 Si no se usa</td><td>✅ Automático</td></tr></tbody></table>

***

### 🎨 Consejos de Mejores Prácticas

#### 💾 Para PERSISTENT:

* ✅ Usa para balances de usuarios
* ✅ Datos que nunca deben perderse
* ❌ Evita para datos temporales (caro)

#### 🏃‍♂️ Para INSTANCE:

* ✅ Configuraciones del contrato
* ✅ Metadatos que cambian ocasionalmente
* ❌ Evita para datos de usuarios individuales

#### ⚡ Para TEMPORARY:

* ✅ Cálculos intermedios
* ✅ Sistema de cooldowns
* ✅ Cache temporal
* ❌ Evita para datos importantes
