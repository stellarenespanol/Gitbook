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

## En proceso...
