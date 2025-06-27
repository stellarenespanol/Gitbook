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

#### 🔗 El Sistema de Doble Enlace

Los NFTs no guardan la imagen directamente en la blockchain. En su lugar, usan un sistema de **doble enlace**:

```
Contrato NFT   
 ↓ (enlace a metadatos)
 Archivo JSON (metadatos)   
 ↓ (enlace a imagen)
 Imagen Real
```

#### 📋 Ejemplo de Metadatos (metadata.json)

```json
{  
  "name": "My NF Token",
  "description": "Cat under Stellar moon",  
  "url": "ipfs://bafkreidfpskdnx45xzzskx5jszsolrw3wfi7hsqkxkpynvhdz6mxs4ck3q",  
  "issuer": "GATTQ6RGZSL3BJG6TMLEENNFHCCUHDZGOJYJ2AMWCJD4H3IEI3CCDESB", 
  "code": "MNFT"
}
```

#### 🎭 Desglosando cada Campo

* 🏷️ **"name"**: "My NF Token" - El nombre específico de este NFT
* 📜 **"description"**: "Cat under Stellar moon" - Descripción poética 🐱🌙
* 🖼️ **"url"**: El enlace **real** a la imagen del gato
* 👨‍💼 **"issuer"**: Dirección de Stellar del creador original
* 🔤 **"code"**: "MNFT" - Símbolo de la colección

#### ✅ Ventajas de Esta Estructura

1. **📈 Escalabilidad**: Puedes agregar más información sin cambiar el contrato
2. **💾 Eficiencia**: El contrato solo guarda UN enlace, no toda la información
3. **🔄 Flexibilidad**: Puedes incluir múltiples imágenes, videos, etc.
4. **📱 Compatibilidad**: Los marketplaces saben exactamente dónde buscar información

### ⚠️Antes de codificar:

Debemos tener la imagen, documento o archivo considerado como pieza a distribuir.\
\
Este archivo se sube en alguno destino en internet, un link público de gdrive o servicio de almacenamiento de archivos .  Un servicio muy bueno es el sistema IPFS el cual garantiza que el archivo siga por mucho tiempo en internet.

Un servicio para subir archivos a IPFS es [https://pinata.cloud/](https://pinata.cloud/)

dentro del archivo metadata.json se puso el url por ipfs que arrojo pinata al subir el archivo y luego dentro del contrato se subió el url por ipfs que arrojó pinata al subir el archivo metada.json

### Código fuente:&#x20;

En el archivo Cargo.toml del directorio raiz. en members, agregamos

```
"examples/mynft"
```

&#x20;

<figure><img src="../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

Dentro de la carpeta examples creamos la carpeta "mynft"



En el folder mynft creamos el archivo Cargo.toml con lo siguiente

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
stellar-default-impl-macro = { workspace = true }

[dev-dependencies]
soroban-sdk = { workspace = true, features = ["testutils"] }
```

Dentro de myt creamos una carpeta llamada src con los siguientes archivos:

contract.rs ( Contrato del token)

lib.rs ( archivo que engancha el contrato y su respectivo test)

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

Escribimos lo siguiente dentro de lib.rs

```rust
#![no_std]
pub mod contract;
```

Código de contract.rs

```rust
use soroban_sdk::{Address, contract, contractimpl, Env, String, Symbol, symbol_short};
use stellar_non_fungible::{
    Base, consecutive::{NonFungibleConsecutive, Consecutive}, ContractOverrides,
    NonFungibleToken
};

const OWNER: Symbol = symbol_short!("OWNER");
const LAST_ID: Symbol = symbol_short!("LAST_ID");
const MAX_SUPPLY: u32 = 10;

#[contract]
pub struct MyNFT;

#[contractimpl]
impl MyNFT {
    pub fn __constructor(e: &Env, owner: Address) {
        Base::set_metadata(e, 
                           String::from_str(e, "ipfs://bafkreiherjgvtailnmiddilp5go7kjt3vgduzxp5k2yujs3uvjfvmgck5e"),
                           String::from_str(e, "My Non Fungible Token"), 
                           String::from_str(e, "MNFT"));
        e.storage().instance().set(&OWNER, &owner);
        // Inicializar el contador de último ID en 0
        e.storage().instance().set(&LAST_ID, &0u32);
    }

    pub fn batch_mint(e: &Env, to: Address, amount: u32) -> u32 {
        let owner: Address = e.storage().instance().get(&OWNER).expect("owner should be set");
        owner.require_auth();
        
        // Verificar límite antes de hacer mint
        let current_last_id = Self::get_last_id(e);
        if current_last_id + amount > MAX_SUPPLY {
            panic!("Cannot mint more than {} NFTs", MAX_SUPPLY);
        }
        
        let result = Consecutive::batch_mint(e, &to, amount);
        
        // Actualizar el último ID
        let new_last_id = current_last_id + amount;
        e.storage().instance().set(&LAST_ID, &new_last_id);
        
        result
    }
    
    pub fn mint(e: &Env, to: Address) -> u32 {
        let owner: Address = e.storage().instance().get(&OWNER).expect("owner should be set");
        owner.require_auth();
        
        // Verificar límite antes de hacer mint
        let current_last_id = Self::get_last_id(e);
        if current_last_id >= MAX_SUPPLY {
            panic!("Cannot mint more than {} NFTs", MAX_SUPPLY);
        }
        
        let result = Base::sequential_mint(e, &to);
        
        // Actualizar el último ID
        let new_last_id = current_last_id + 1;
        e.storage().instance().set(&LAST_ID, &new_last_id);
        
        result
    }
    
    // Nueva función para obtener el último ID minted
    pub fn get_last_id(e: &Env) -> u32 {
        e.storage().instance().get(&LAST_ID).unwrap_or(0)
    }
    
    // Nueva función para obtener cuántos NFTs quedan por mintear
    pub fn remaining_supply(e: &Env) -> u32 {
        let current_last_id = Self::get_last_id(e);
        if current_last_id >= MAX_SUPPLY {
            0
        } else {
            MAX_SUPPLY - current_last_id
        }
    }
    
    // Nueva función para obtener el supply máximo
    pub fn max_supply(e: &Env) -> u32 {
        MAX_SUPPLY
    }
    
    // Nueva función para obtener el supply total actual
    pub fn total_supply(e: &Env) -> u32 {
        Self::get_last_id(e)
    }
}

#[contractimpl]
impl NonFungibleToken for MyNFT {
    type ContractType = Consecutive;

    fn owner_of(e: &Env, token_id: u32) -> Address {
        Self::ContractType::owner_of(e, token_id)
    }

    fn transfer(e: &Env, from: Address, to: Address, token_id: u32) {
        Self::ContractType::transfer(e, &from, &to, token_id);
    }

    fn transfer_from(e: &Env, spender: Address, from: Address, to: Address, token_id: u32) {
        Self::ContractType::transfer_from(e, &spender, &from, &to, token_id);
    }

    fn balance(e: &Env, owner: Address) -> u32 {
        Self::ContractType::balance(e, &owner)
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


#[contractimpl]
impl NonFungibleConsecutive for MyNFT {}
```

### 🧩 Estructura del Código

#### 1. 📦 Importaciones y Configuración Inicial

```rust
use soroban_sdk::{Address, contract, contractimpl, Env, String, Symbol, symbol_short};use stellar_non_fungible::{    Base, consecutive::{NonFungibleConsecutive, Consecutive}, ContractOverrides,    NonFungibleToken};
```

**🔍 ¿Qué significa esto?**

* Importamos las herramientas de Soroban (como en tokens fungibles) ✅
* Pero también importamos las librerías especiales para NFTs de OpenZeppelin 🎯
* `NonFungibleToken` es como el "ERC20" pero para NFTs 📝

#### 2. 🏗️ Constantes del Contrato

<pre class="language-rust"><code class="lang-rust"><strong>// 👑 Quién puede crear NFTs
</strong>const OWNER: Symbol = symbol_short!("OWNER");     
// 🔢 Contador de NFTs creados
const LAST_ID: Symbol = symbol_short!("LAST_ID"); 
// 🚫 Máximo 10 NFTs
const MAX_SUPPLY: u32 = 10;                      
</code></pre>

**💡 Explicación Simple:**

* `OWNER`: Como tener las llaves de la casa 🗝️
* `LAST_ID`: Un contador que dice "ya hiciste 5 NFTs" 📊
* `MAX_SUPPLY`: Como decir "solo habrá 10 cartas Pokémon de esta edición" 🎴

#### 3. 🎯 Constructor - Configuración Inicial

```rust
pub fn __constructor(e: &Env, owner: Address) {   
       Base::set_metadata(e,
                    String::from_str(e, "ipfs://bafkreih..."), // 🖼️ Link del archivo metadata.json                     
                    String::from_str(e, "My Non Fungible Token"), // 📛 Nombre     
                    String::from_str(e, "MNFT")
                   );                // 🏷️ Símbolo      
       // 👑 Guardar el dueño
       e.storage().instance().set(&OWNER, &owner);   
       // 🔢 Empezar contador en 0
       e.storage().instance().set(&LAST_ID, &0u32); 
}
```

**🚀 ¿Qué hace?**

* Define la ruta del archivo json con la medata data y datos del NFT 🏪
* Establece quién puede crear nuevos NFTs 👨‍🎨
* Pone el contador en cero (aún no hay NFTs) 0️⃣

#### 4. 🎨 Función `mint` - Crear UN NFT

```rust
pub fn mint(e: &Env, to: Address) -> u32 {
  // 🔐 Solo el dueño puede crear NFTs    
  let owner: Address = e.storage().instance().get(&OWNER).expect("owner should be set");   
  owner.require_auth();    
  // 🚦 ¿Ya llegamos al límite? 
  let current_last_id = Self::get_last_id(e);
      if current_last_id >= MAX_SUPPLY {   
           panic!("Cannot mint more than {} NFTs", MAX_SUPPLY);   
      }        
      // ✨ ¡Crear el NFT!   
       let result = Base::sequential_mint(e, &to);      
       // 📈 Actualizar contador   
       let new_last_id = current_last_id + 1; 
       e.storage().instance().set(&LAST_ID, &new_last_id);     
       // 🎁 Devolver el ID del NFT creado
       result 
 }
```

**🔥 Paso a paso:**

1. **Verificar permisos**: ¿Eres el dueño? 🔍
2. **Contar NFTs**: ¿Ya hiciste 10? ⚠️
3. **Crear NFT**: ¡Abracadabra! ✨
4. **Actualizar contador**: Ahora tienes uno más 📊
5. **Devolver ID**: Te digo qué número es tu NFT 🏷️

#### 5. 🚀 Función `batch_mint` - Crear VARIOS NFTs

```rust
pub fn batch_mint(e: &Env, to: Address, amount: u32) -> u32 { 
       let owner: Address = e.storage().instance().get(&OWNER).expect("owner should be set");  
       owner.require_auth();      
       // 🧮 ¿Si creo X cantidad, me paso del límite?  
       let current_last_id = Self::get_last_id(e);  
         if current_last_id + amount > MAX_SUPPLY {   
            panic!("Cannot mint more than {} NFTs", MAX_SUPPLY); 
         } 
         // 🏭 Crear muchos NFTs de una vez  
         let result = Consecutive::batch_mint(e, &to, amount);  
         // 📊 Actualizar contador con la cantidad total    
         let new_last_id = current_last_id + amount;
         e.storage().instance().set(&LAST_ID, &new_last_id);    
         result
}
```

**💪 ¿Cuándo usarla?**

* Quieres dar 3 NFTs a alguien de una vez 🎁🎁🎁
* Es más eficiente que llamar `mint()` 3 veces ⚡
* Perfecta para lanzamientos masivos 🚀

#### 6. 📊 Funciones de Consulta - Ver el Estado

```rust
// 🔢 ¿Cuántos NFTs ya se crearon?
pub fn get_last_id(e: &Env) -> u32 {
    e.storage().instance().get(&LAST_ID).unwrap_or(0)
}

// 📈 Lo mismo que arriba, pero con nombre más claro
pub fn total_supply(e: &Env) -> u32 {
    Self::get_last_id(e)
}

// 🎯 ¿Cuántos NFTs puedo crear todavía?
pub fn remaining_supply(e: &Env) -> u32 { 
   let current_last_id = Self::get_last_id(e);  
   if current_last_id >= MAX_SUPPLY {
      //  Ya no quedan
      0      
    } 
    else { 
          // ✅ Quedan X 
         MAX_SUPPLY - current_last_id    
         }
 }
 // 🔝 ¿Cuál es el máximo total?
 pub fn max_supply(e: &Env) -> u32 {  
   MAX_SUPPLY
 }
```

**🎮 Ejemplo práctico:**

* Creaste 7 NFTs ➡️ `total_supply()` = 7
* Máximo son 10 ➡️ `max_supply()` = 10
* Te quedan 3 ➡️ `remaining_supply()` = 3

#### 7. 🛠️ Implementación del Trait NFT

```rust
#[contractimpl]
impl NonFungibleToken for MyNFT { 
    // 🏭 Usamos el tipo "consecutivo"
   type ContractType = Consecutive; 
    // 👤 ¿De quién es este NFT? 
    fn owner_of(e: &Env, token_id: u32) -> Address { ... }     
    // 📦 ¿Cuántos NFTs tiene esta persona?  
    fn balance(e: &Env, owner: Address) -> u32 { ... }    
    // 🔄 Transferir NFT a otra persona 
    fn transfer(e: &Env, from: Address, to: Address, token_id: u32) { ... }   
   // ... más funciones estándar
}
```

**🎯 ¿Qué significa `Consecutive`?**

* Los NFTs se crean en orden: 1, 2, 3, 4... 📊
* Es más eficiente para crear muchos NFTs seguidos ⚡
* Perfecto para colecciones numeradas 🎴

### Compilación del contrato

Nos ubicamos dentro de ../stellar-contracts\examples\mynft allí en consola ejecutamos:

```bash
cargo build --target wasm32-unknown-unknow
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Despliegue del contrato

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

```bash
stellar contract deploy *
  --wasm ../../target/wasm32-unknown-unknown/release/mynft.wasm *
  --source <cuenta seleccionada> *
  --network testnet *
  --alias mynft  *
  -- *
  --owner <cuenta habilitada para mint>
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Función mint

Con una cuenta no autorizada

```
stellar contract invoke `
--id mynft `
--source <cuento sin permisos> `
--network testnet `
-- `
mint `
--to <cuenta destino>
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Con la cuenta owner

```
stellar contract invoke `
--id mynft `
--source <cuenta con permisos> `
--network testnet `
-- `
mint `
--to <cuenta destino>
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
