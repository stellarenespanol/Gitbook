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

### Código fuente:

```rust
//! Non-Fungible Vanilla Example Contract.
//!
//! Demonstrates an example usage of the NFT default base implementation.

use soroban_sdk::{contract, contractimpl, contracttype, Address, Env, String};
use stellar_default_impl_macro::default_impl;
use stellar_non_fungible::{burnable::NonFungibleBurnable, Base, NonFungibleToken};

#[contracttype]
pub enum DataKey {
    Owner,
}

#[contract]
pub struct ExampleContract;

#[contractimpl]
impl ExampleContract {
    pub fn __constructor(e: &Env, owner: Address) {
        e.storage().instance().set(&DataKey::Owner, &owner);
        Base::set_metadata(
            e,
            String::from_str(e, "ipfs://bafkreiherjgvtailnmiddilp5go7kjt3vgduzxp5k2yujs3uvjfvmgck5e"),
            String::from_str(e, "My Non Fungible Token"),
            String::from_str(e, "MNFT"),
        );
    }

    pub fn mint(e: &Env, to: Address) -> u32 {
        let owner: Address =
            e.storage().instance().get(&DataKey::Owner).expect("owner should be set");
        owner.require_auth();
        let token_id: u32 = Base::sequential_mint(e, &to);
        if token_id > 11 {
            panic!("Maximum minted añready");
        }
        token_id
    }
}

#[default_impl]
#[contractimpl]
impl NonFungibleToken for ExampleContract {
    type ContractType = Base;
}

#[default_impl]
#[contractimpl]
impl NonFungibleBurnable for ExampleContract {}
```

Se transformó un poco el código dentro de examples/nft-sequential-minting

dentro de la carpeta src se creó el archivo metadata.json con la siguiente información:

```json
{
  "name": "My NF Token",
  "description": "Cat under Stellar moon",
  "url": "ipfs://bafkreidfpskdnx45xzzskx5jszsolrw3wfi7hsqkxkpynvhdz6mxs4ck3q",
  "issuer": "GATTQ6RGZSL3BJG6TMLEENNFHCCUHDZGOJYJ2AMWCJD4H3IEI3CCDESB",
  "code": "MNFT"
}
```

### 💻  Análisis del Código NFT

#### 🏗️ Estructura Básica del Contrato

```rust
#[contract]
pub struct ExampleContract;
```

Esto es como decir "Hola mundo, soy un contrato inteligente llamado ExampleContract" 👋

#### 🔑 Sistema de Llaves de Datos

```rust
#[contracttype]
pub enum    DataKey {
                Owner,
            }
```

Es como tener un llavero 🗝️ donde guardamos información importante. En este caso, solo guardamos quién es el "Owner" (propietario) del contrato.

#### 🚀 Constructor - El Nacimiento del Contrato

```rust
pub fn __constructor(e: &Env, owner: Address) { 
   e.storage().instance().set(&DataKey::Owner, &owner);  
     Base::set_metadata(    
             e,        
             // 👇 Este enlace apunta al archivo JSON con TODA la info 
             String::from_str(e, "ipfs://bafkreiherjgvtailnmiddilp5go7kjt3vgduzxp5k2yujs3uvjfvmgck5e"),    
             // Nombre de la colección
             String::from_str(e, "My Non Fungible Token"),  
             // Símbolo de la colección 
             String::from_str(e, "MNFT"),    
             );
}
```

**¿Qué hace esto?** 🤷‍♀️

1. **Guarda el propietario**: Como ponerle una etiqueta con el nombre del dueño
2. **Establece metadatos**:
   * 📄 **Enlace JSON**: Dirección IPFS que apunta al archivo metadata.json
   * 📛 **Nombre de colección**: "My Non Fungible Token"
   * 🏷️ **Símbolo**: "MNFT" (como las siglas de una empresa)

#### 🎨 Función Mint - Crear NFTs

```rust
pub fn mint(e: &Env, to: Address) -> u32 {
    let owner: Address = e.storage().instance().get(&DataKey::Owner).expect("owner should be set");  
    owner.require_auth();
    let token_id: u32 = Base::sequential_mint(e, &to);    if token_id > 11 {        panic!("Maximum minted añready");    }    token_id}
```

**Paso a paso:** 👣

1. **Verificar propietario**: "¿Eres realmente el dueño?" 🕵️
2. **Pedir autorización**: Como pedir la contraseña 🔐
3. **Crear NFT**: Le asigna un número único (1, 2, 3, etc.) 🔢
4. **Límite de creación**: Solo permite crear hasta 12 NFTs (0-11) ⚠️
5. **Devuelve el ID**: Te dice qué número de NFT creaste 📋

#### 🔥 Funcionalidades Adicionales

```rust
#[default_impl]
#[contractimpl]
impl NonFungibleToken for ExampleContract { 
   type ContractType = Base;
}#[default_impl]#[contractimpl]
impl NonFungibleBurnable for ExampleContract {}
```

**¿Qué significa esto?** 🧐

* **NonFungibleToken**: Le da todas las funciones básicas de un NFT (transferir, ver propietario, etc.)
* **NonFungibleBurnable**: Permite "quemar" 🔥 NFTs (destruirlos permanentemente)

***

### 🎮 Parte 4: Analogía con el Mundo Real

Imagina que este contrato es como una **máquina expendedora de cartas coleccionables**:

* 👨‍💼 **Owner**: El dueño de la máquina
* 🎫 **Mint**: Crear una nueva carta (solo el dueño puede hacerlo)
* 🔢 **Token ID**: Cada carta tiene un número único
* 📦 **Máximo 12**: Solo puede crear 12 cartas en total
* 🔥 **Burn**: Puedes destruir tu carta si quieres
* 📋 **Metadatos**: Como la "ficha técnica" de cada carta que incluye nombre, descripción e imagen

***

### 🔄 Parte 5: Flujo Completo de Funcionamiento

#### 📊 Cuando alguien ve tu NFT en un marketplace:

1. 🔍 **Marketplace lee el contrato**: "Este NFT tiene metadatos en bafkrei..."
2. 📄 **Descarga el JSON**: Lee el nombre, descripción, etc.
3. 🖼️ **Muestra la imagen**: Usando la URL del campo "url"
4. ✨ **Presenta todo junto**: Nombre + descripción + imagen = NFT completo
