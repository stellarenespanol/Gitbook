# Tokens

## 🌟 Introducción a los Tokens SEP-41 en Stellar

### 🤔 ¿Qué es un Token SEP-41?

El **SEP-41** es un estándar para tokens **fungibles** en la red Stellar que utiliza contratos inteligentes Soroban. Pero, ¿qué significa "fungible"? 🤷‍♂️

#### 🪙 Fungibilidad Explicada de Forma Simple

Imagina que tienes billetes de $10 pesos en tu billetera. Cada billete de $10 es **intercambiable** por cualquier otro billete de $10 - todos tienen el mismo valor y función. Esto es **fungibilidad**: cada unidad es idéntica e intercambiable con otra del mismo tipo.

**Pero aquí viene algo súper importante:** Los tokens fungibles también son **divisibles** ✂️

#### 🔢 Divisibilidad y Unidad Mínima

Al igual que el dinero físico, los tokens fungibles pueden dividirse en partes más pequeñas, pero **siempre hay un límite**:

**💰 Ejemplo con dinero físico:**

* 1 peso se puede dividir en 100 centavos
* **El centavo es la unidad mínima** - no puedes tener 0.5 centavos físicos

**🪙 Ejemplo con tokens digitales:**

* 1 token puede dividirse según sus **decimales** configurados
* Si un token tiene 6 decimales: 1 token = 1,000,000 unidades mínimas
* Si un token tiene 2 decimales: 1 token = 100 unidades mínimas

**🧮 Casos prácticos:**

* **Bitcoin**: 8 decimales → 1 BTC = 100,000,000 satoshis
* **USDC**: 6 decimales → 1 USDC = 1,000,000 micro-USDC
* **Tu token**: Tú decides cuántos decimales → flexibilidad total

**⚠️ Regla de oro:** No puedes tener fracciones de la unidad mínima. Si tu token tiene 2 decimales, no puedes enviar 0.001 tokens (necesitarías al menos 0.01).

**Ejemplos de tokens fungibles:**

* 💰 Monedas digitales (como USDC, Bitcoin)
* 💳 Puntos de recompensa
* 🎮 Monedas de videojuegos
* 📈 Tokens de inversión

**¿Por qué son importantes?** Los tokens fungibles son perfectos para crear:

* 💱 Monedas digitales
* 🏦 Sistemas de pagos
* 💸 Programas de lealtad
* 📊 Activos financieros

### 🧙‍♂️ OpenZeppelin: Tu Escudo de Seguridad

#### 🛡️ ¿Por qué usar OpenZeppelin?

OpenZeppelin es como tener un **equipo de expertos en seguridad** trabajando para ti 24/7. Aquí te explico por qué es crucial:

**🔐 Contratos Ultra-Seguros:**

* ⚔️ **Batalla-probados**: Millones de dólares protegidos en producción
* 🧪 **Testeo exhaustivo**: Cada línea de código probada por expertos
* 🏆 **Estándar de la industria**: Usado por los proyectos más grandes
* 👥 **Revisión comunitaria**: Miles de desarrolladores revisando el código

**💰 ¿Sabes cuánto dinero se ha perdido por contratos inseguros?**

* 🔥 **+$3 billones USD** perdidos en hacks de DeFi desde 2020
* 💸 **The DAO Hack (2016)**: $60 millones robados
* 🚨 **Poly Network (2021)**: $600 millones hackeados
* ⚠️ **Y muchos más**...

**🛠️ El Trabajo Pesado Ya Está Hecho:** OpenZeppelin hace el trabajo que **tú no quieres (ni debes) hacer**:

* 🔒 **Protección contra reentrancy attacks**
* 🛡️ **Validación de permisos y roles**
* ⚡ **Optimización de gas**
* 🧩 **Compatibilidad con estándares**
* 🔧 **Patrones de actualización seguros**
* 🚦 **Controles de acceso robustos**

***

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

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>Vista breve del archivo Cargo.toml</p></figcaption></figure>

Para cada ejemplo, por facilidad vamos a crear una subcarpeta &#x20;

### Ejemplo:

Vamos a crear el token MYT (My token).

En el archivo Cargo.toml del directorio raiz. en members, agregamos&#x20;

```
"examples/myt"
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Dentro de la carpeta examples creamos la carpeta "myt"



En el folder myt creamos el archivo Cargo.toml con lo siguiente

```toml
[package]
name = "myt"
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
stellar-access-control = { workspace = true }
stellar-access-control-macros = { workspace = true }
stellar-fungible = { workspace = true }
stellar-default-impl-macro = { workspace = true }

[dev-dependencies]
soroban-sdk = { workspace = true, features = ["testutils"] }
```

En el caso de ser un token fungible es con&#x20;

```toml
stellar-fungible = { workspace = true }
```

En el caso de ser un NFT se pone

```toml
stellar-non-fungible = { workspace = true }
```

Dentro de myt creamos una carpeta llamada src con los siguientes archivos:

contract.rs ( Contrato del token)

lib.rs ( archivo que engancha el contrato y su respectivo test)

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Estructura de la carpeta myt</p></figcaption></figure>



En proceso....
