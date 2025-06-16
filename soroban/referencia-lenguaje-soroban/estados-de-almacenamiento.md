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

**Mac/Linux**

```bash
stellar contract deploy \
--wasm target\wasm32v1-none\release\persistent.wasm \
--source developer \
--network testnet \
--alias events
```

**Windows**

```bash
stellar contract deploy `
--wasm target\wasm32v1-none\release\persistent.wasm `
--source developer `
--network testnet `
--alias events
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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p>Resultado de la compilación</p></figcaption></figure>

**Despliegue del contrato**&#x20;

**Mac/Linux**

```bash
stellar contract deploy \
--wasm target\wasm32v1-none\release\instance.wasm
--source developer \
--network testnet \
--alias events
```

**Windows**

```bash
stellar contract deploy `
--wasm target\wasm32v1-none\release\instance.wasm `
--source developer `
--network testnet `
--alias events
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Resultado del despliegue</p></figcaption></figure>

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
