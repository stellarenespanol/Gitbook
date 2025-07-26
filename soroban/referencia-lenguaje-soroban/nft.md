# NFT

### ¿Qué es un Token No Fungible (NFT)?

#### 🌟 Conceptos Básicos

Un **Token No Fungible (NFT)** es como un certificado digital único e irrepetible. Imagínate que tienes una obra de arte física: no puedes dividirla en pedacitos y cada una es única. Los NFTs funcionan igual pero en el mundo digital.

#### 🤔 ¿Qué significa "No Fungible"?

* **💰 Fungible**: Como el dinero. Un billete de $1000 es igual a otro billete de $1000, son intercambiables.
* **🎨 No Fungible**: Como una pintura original. La Mona Lisa es única, no puedes cambiarla por otra pintura y que valga exactamente lo mismo.

#### 🌟 ¿Por Qué son Importantes los NFTs?

Los NFTs permiten:

* ✅ **Propiedad digital verificable**: Puedes demostrar que algo digital es tuyo
* 🎨 **Arte digital**: Artistas pueden vender obras digitales únicas
* 🎮 **Gaming**: Objetos únicos en videojuegos
* 📜 **Certificados**: Diplomas, licencias, etc.



### 📚 Diferencias clave con Tokens Fungibles

| Tokens Fungibles 🪙      | NFTs 🎨                     |
| ------------------------ | --------------------------- |
| Todos son iguales        | Cada uno es único           |
| Se pueden dividir        | No se pueden dividir        |
| Ejemplo: 1 USDC = 1 USDC | Cada NFT tiene su propio ID |

### 🏗️  Estructura de un NFT - Los Metadatos

#### 🧠 ¿Qué significa que un NFT use un "archivo JSON en lugar de la ubicación directa de la imagen"?

Cuando creas un NFT, es común pensar que la imagen (como un JPG o PNG) **se guarda directamente en la blockchain**, pero **eso no es así**. Por razones de costo y eficiencia, **no se suben archivos pesados a la blockchain**, sino que se usa un **sistema de doble enlace** (también llamado "two-step metadata linking").

**🔗 ¿Qué es el sistema de doble enlace?**

Es una estructura donde el contrato del NFT **no apunta directamente a la imagen**, sino a un archivo `.json` que contiene los **metadatos** del NFT. Ese archivo `.json`, a su vez, contiene un enlace a la imagen u otros recursos.

Veámoslo como una cadena:

```
scssCopiarEditarContrato NFT
     ↓ (apunta a)
Archivo JSON (metadatos)
     ↓ (apunta a)
Imagen real (JPG, GIF, video, etc.)
```

**📁 ¿Por qué no enlazar directamente la imagen desde el contrato?**

Porque al enlazar a un archivo `.json`, tienes varias **ventajas importantes**:

1. **Puedes incluir mucha más información**, no solo la imagen.
2. **Es más flexible**: si luego quieres mostrar un video, animación o cambiar la descripción, lo puedes hacer sin tocar el contrato.
3. **Es más económico y limpio**: solo apuntas a un archivo liviano con metadatos.

***

#### 📄 Ejemplo claro de un archivo `metadata.json`

```json
jsonCopiarEditar{
  "name": "My NF Token",
  "description": "Cat under Stellar moon",
  "url": "ipfs://bafkreidfpskdnx45xzzskx5jszsolrw3wfi7hsqkxkpynvhdz6mxs4ck3q",
  "issuer": "GATTQ6RGZSL3BJG6TMLEENNFHCCUHDZGOJYJ2AMWCJD4H3IEI3CCDESB",
  "code": "MNFT"
}
```

Este archivo puede estar alojado en IPFS u otro sistema descentralizado, y contiene:

* `name`: El nombre visible del NFT.
* `description`: Una breve descripción o historia.
* `url`: El enlace real a la imagen del NFT.
* `issuer`: La dirección del creador en Stellar.
* `code`: El símbolo o identificador del NFT o colección.

***

#### ✅ Ventajas clave de esta arquitectura

| Beneficio          | Descripción                                                                  |
| ------------------ | ---------------------------------------------------------------------------- |
| **Escalabilidad**  | Puedes agregar más campos al `.json` en el futuro sin modificar el contrato. |
| **Eficiencia**     | El contrato solo guarda una línea (el enlace al `.json`), ahorrando espacio. |
| **Flexibilidad**   | Puedes usar imágenes, videos, animaciones, incluso archivos 3D.              |
| **Compatibilidad** | Los marketplaces ya saben cómo leer estos archivos `.json`.                  |

Para este ejemplo usamos el servicio de [https://pinata.cloud/](https://pinata.cloud/),  primero se sube la imagen como tal, para tener su CID , ese lo incluimos en el archivo de metadata.json y luego subimos el archivo metadata.json

<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

#### 🧙‍♂️ OpenZeppelin Wizard para Stellar

OpenZeppelin ha creado un **wizard** (asistente) súper útil para generar tokens SEP-41: **🔗** [**https://wizard.openzeppelin.com/stellar#**](https://wizard.openzeppelin.com/stellar)

**🌟 ¿Qué hace el wizard por ti?**

* 🎨 **Interfaz visual**: Sin necesidad de escribir código desde cero
* ⚙️ **Configuración simple**: Solo selecciona las funciones que necesitas
* 📋 **Código generado**: Listo para usar en minutos
* 🔧 **Personalizable**: Agrega tus propias funciones después

#### ⚠️ Importante: Limitaciones Actuales

Aunque el wizard es genial para empezar, **todavía tiene algunos errores** 🐛. Por eso, es mejor usar los **ejemplos oficiales** como referencia:

**🔗** [**https://github.com/OpenZeppelin/stellar-contracts/**](https://github.com/OpenZeppelin/stellar-contracts/)

***

**Instructivo para poder utilizar el código de OpenZeppelin**

#### Pre-requisitos

1. Tener instalado Rust.
2. Tener instalado El cliente Stellar
3. tener instalado el cliente de GIt



* Copiar el repositorio de github de OpenZeppelin en una carpeta previamente seleccionada con la siguiente instrucción.

```bash
git clone https://github.com/OpenZeppelin/stellar-contracts.git
```

&#x20;

* Con el editor favorito de código abrimos el folder stellar-contracts.
* En el directorio raiz abrir el archivo Cargo.toml

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>Vista breve del archivo Cargo.toml</p></figcaption></figure>

Para el  ejemplo vamos a crear una subcarpeta dentro de examples

### Ejemplo :NFT con consecutivo.

Una vez creado el contrato del NFT como tal , se hace el proceso de acuñación del NFT se genera un consecutivo.

Primero se copia la carpeta nft-consecutive

<figure><img src="../../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

Copiamos la carpeta y la nueva carpeta lo nombramos como mynft

<figure><img src="../../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

Dentro de mynft editados el archivo Cargo.toml

editamos el archivo.

```toml
[package]
name = "mynft"
edition.workspace = true
license.workspace = true
repository.workspace = true
publish = false
version.workspace = true

[lib]
crate-type = ["cdylib"]
doctest = false

[dependencies]
soroban-sdk = { workspace = true }
stellar-non-fungible = { workspace = true }

[dev-dependencies]
soroban-sdk = { workspace = true, features = ["testutils"] }
```

Antes de proceder con el código añadimos la ruta de mynft dentro del archivo  Cargo.toml en el directorio raiz

<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

Escribimos lo siguiente dentro de lib.rs

```rust
#![no_std]
pub mod contract;
```

Código de contract.rs

```rust
//! Non-Fungible Consecutive Example Contract.
//!
//! Demonstrates an example usage of the Consecutive extension, enabling
//! efficient batch minting in a single transaction.

use soroban_sdk::{contract, contractimpl, contracttype, Address, Env, String};
use stellar_non_fungible::{
    burnable::NonFungibleBurnable,
    consecutive::{Consecutive, NonFungibleConsecutive},
    Base, ContractOverrides, NonFungibleToken,
};

#[contracttype]
pub enum DataKey {
    Owner,
}

#[contract]
pub struct mynft;

#[contractimpl]
impl mynft {
    pub fn __constructor(e: &Env, owner: Address) {
        e.storage().instance().set(&DataKey::Owner, &owner);
        Base::set_metadata(
            e,
            String::from_str(e, "bafkreiherjgvtailnmiddilp5go7kjt3vgduzxp5k2yujs3uvjfvmgck5e"),
            String::from_str(e, "My NFT"),
            String::from_str(e, "NFTKN"),
        );
    }

    pub fn batch_mint(e: &Env, to: Address, amount: u32) -> u32 {
        let owner: Address =
            e.storage().instance().get(&DataKey::Owner).expect("owner should be set");
        owner.require_auth();
        Consecutive::batch_mint(e, &to, amount)
    }
}

// You don't have to provide the implementations for all the methods,
// `#[default_impl]` macro does this for you. This example showcases
// what is happening under the hood when you use `#[default_impl]` macro.
#[contractimpl]
impl NonFungibleToken for ExampleContract {
    type ContractType = Consecutive;

    fn balance(e: &Env, owner: Address) -> u32 {
        Self::ContractType::balance(e, &owner)
    }

    fn owner_of(e: &Env, token_id: u32) -> Address {
        Self::ContractType::owner_of(e, token_id)
    }

    fn transfer(e: &Env, from: Address, to: Address, token_id: u32) {
        Self::ContractType::transfer(e, &from, &to, token_id);
    }

    fn transfer_from(e: &Env, spender: Address, from: Address, to: Address, token_id: u32) {
        Self::ContractType::transfer_from(e, &spender, &from, &to, token_id);
    }

    fn approve(
        e: &Env,
        approver: Address,
        approved: Address,
        token_id: u32,
        live_until_ledger: u32,
    ) {
        Self::ContractType::approve(e, &approver, &approved, token_id, live_until_ledger);
    }

    fn approve_for_all(e: &Env, owner: Address, operator: Address, live_until_ledger: u32) {
        Self::ContractType::approve_for_all(e, &owner, &operator, live_until_ledger);
    }

    fn get_approved(e: &Env, token_id: u32) -> Option<Address> {
        Self::ContractType::get_approved(e, token_id)
    }

    fn is_approved_for_all(e: &Env, owner: Address, operator: Address) -> bool {
        Self::ContractType::is_approved_for_all(e, &owner, &operator)
    }

    fn name(e: &Env) -> String {
        Self::ContractType::name(e)
    }

    fn symbol(e: &Env) -> String {
        Self::ContractType::symbol(e)
    }

    fn token_uri(e: &Env, token_id: u32) -> String {
        Self::ContractType::token_uri(e, token_id)
    }
}

impl NonFungibleConsecutive for mynft {}

#[contractimpl]
impl NonFungibleBurnable for mynft {
    fn burn(e: &Env, from: Address, token_id: u32) {
        Self::ContractType::burn(e, &from, token_id);
    }

    fn burn_from(e: &Env, spender: Address, from: Address, token_id: u32) {
        Self::ContractType::burn_from(e, &spender, &from, token_id);
    }
}
```

## 🖼️ Explicación del Contrato mynft (NFT Consecutivo)

#### ¿Qué es este contrato?

Este contrato crea un **token no fungible (NFT)** en la blockchain de Stellar, usando el módulo _Consecutive_, que permite **crear muchos NFTs de forma eficiente en una sola transacción**. Además, los NFTs pueden **quemarse** (eliminarlos para siempre).

🧰 1. Importaciones: Herramientas necesarias

```rust
use soroban_sdk::{contract, contractimpl, contracttype, Address, Env, String};
use stellar_non_fungible::{
    burnable::NonFungibleBurnable,
    consecutive::{Consecutive, NonFungibleConsecutive},
    Base, ContractOverrides, NonFungibleToken,
};
```

#### ¿Qué significa?

* `soroban_sdk`: Caja de herramientas básica para contratos Soroban.
* `stellar_non_fungible`: Librería oficial de Stellar para trabajar con NFTs.
  * `burnable`: Permite destruir (quemar) NFTs.
  * `consecutive`: Permite crear muchos NFTs de forma consecutiva (por lotes).
  * `Base`, `NonFungibleToken`: Funciones estándar para manejar NFTs.

🧱 2. Definición del contrato

```rust
#[contracttype]
pub enum DataKey {
    Owner,
}
```

#### Para qué sirve esto?

* Define una **clave personalizada** (`Owner`) para guardar el dueño del contrato en el almacenamiento interno del contrato.
* Es útil para que solo ese dueño pueda ejecutar ciertas funciones como "mint".

### 🧱 3. Declaración del contrato

```rust
#[contract]
pub struct mynft;
```

* `mynft` es el nombre del contrato.
* Con `#[contract]` le decimos a Soroban que esto es un contrato inteligente.

***

### 🏗️ 4. Constructor (\_\_constructor)

```rust
pub fn __constructor(e: &Env, owner: Address) {
    e.storage().instance().set(&DataKey::Owner, &owner);
    Base::set_metadata(e, String::from_str(e, "CID"), String::from_str(e, "My NFT"), String::from_str(e, "NFTKN"));
}
```

#### ¿Qué hace?

1. Guarda el **dueño del contrato** (quien puede mintear NFTs).
2. Define la **metadata básica**:
   * `"My NFT"`: nombre del token.
   * `"NFTKN"`: símbolo del token.
   * `"CID"`: Dirección de contenido (por ejemplo, un enlace IPFS con más datos visuales o técnicos del NFT).

### 🧯 5. Función batch\_mint (minteo en lote)

```rust
fn batch_mint(e: &Env, to: Address, amount: u32) -> u32 {
    let owner: Address = e.storage().instance().get(...).expect(...);
    owner.require_auth();
    Consecutive::batch_mint(e, &to, amount)
}
```

#### ¿Qué hace?

* Solo el **dueño original** del contrato puede mintear NFTs.
* Permite crear varios NFTs (según el `amount`) de forma eficiente.
* Usa la función `batch_mint` del módulo `Consecutive`.

💡 _Ejemplo:_\
Si llamas `batch_mint(to, 5)`, se crean 5 NFTs y se asignan al usuario `to`.

### 🧾 6. Implementación estándar de NonFungibleToken

Esta parte define todas las funciones necesarias para que el NFT funcione según los estándares:

```rust
impl NonFungibleToken for mynft {
    ...
}
```

#### ¿Qué funciones se incluyen?

🔎 **Consultas:**

* `balance()`: ¿Cuántos NFTs tiene un usuario?
* `owner_of(token_id)`: ¿Quién es el dueño de un NFT específico?
* `name()`, `symbol()`, `token_uri(token_id)`: Metadata del NFT.

🔄 **Transferencias:**

* `transfer()`: Transferir un NFT.
* `transfer_from()`: Transferencia con aprobación previa.
* `approve()`, `approve_for_all()`: Dar permisos a otros.
* `get_approved()`, `is_approved_for_all()`: Consultar permisos.

***

### 🔥 7. Funciones para quemar NFTs (Burnable)

```rust
impl NonFungibleBurnable for mynft {
    fn burn(...);
    fn burn_from(...);
}
```

#### ¿Qué hacen?

* `burn(from, token_id)`: El dueño puede destruir su NFT.
* `burn_from(spender, from, token_id)`: Un tercero autorizado puede quemar un NFT en nombre del dueño.

💡 _Esto sirve para eliminar NFTs obsoletos o por decisión del usuario._

***

### ✅ 8. Consecutive extension

```rust
impl NonFungibleConsecutive for mynft {}
```

Esto indica que el contrato utiliza el módulo _Consecutive_, que permite:

* Mintear muchos NFTs seguidos.
* Optimizar almacenamiento y eficiencia.

***

### 🧠 Analogía general

* Piensa en este contrato como una **fábrica de obras de arte digitales** que puede crear muchas piezas a la vez, entregarlas a quien tú quieras, y también destruirlas si ya no quieres que existan.
* Todo está bajo el control del **creador original (owner)**.

**EN PROCESO**



