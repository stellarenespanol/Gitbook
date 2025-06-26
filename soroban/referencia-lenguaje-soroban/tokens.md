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

***

### Ejemplo 1: _Un token sencillo_

Vamos a crear el token MYT (My token).

MyToken usa la funcionalidad limitada ( _**capped**_ ) de un token fungible,  en esta ocasión la función _**set\_cap**_, en esta le indicamos cual es el maximo de monedas posibles a generar del token.

En el archivo Cargo.toml del directorio raiz. en members, agregamos&#x20;

```
"examples/myt"
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption><p>Estructura para el token Myt</p></figcaption></figure>

Escribimos lo siguiente dentro de lib.rs

```rust
#![no_std]
pub mod contract;
```

Código de contract.rs

```rust
use soroban_sdk::{contract, contractimpl, Address, Env, String};
use stellar_fungible::{
    capped::{check_cap, set_cap},
    Base, FungibleToken,
};

#[contract]
pub struct MyT;

#[contractimpl]
impl MyT {
    pub fn __constructor(e: &Env, cap: i128) {
        Base::set_metadata(e, 2, String::from_str(e, "MyToken"), String::from_str(e, "MYT"));
        set_cap(e, cap);
    }

    pub fn mint(e: &Env, account: Address, amount: i128) {
        check_cap(e, amount);
        Base::mint(e, &account, amount);
    }
}

#[contractimpl]
impl FungibleToken for MyT {
    type ContractType = Base;

    fn total_supply(e: &Env) -> i128 {
        Self::ContractType::total_supply(e)
    }

    fn balance(e: &Env, account: Address) -> i128 {
        Self::ContractType::balance(e, &account)
    }

    fn allowance(e: &Env, owner: Address, spender: Address) -> i128 {
        Self::ContractType::allowance(e, &owner, &spender)
    }

    fn transfer(e: &Env, from: Address, to: Address, amount: i128) {
        Self::ContractType::transfer(e, &from, &to, amount);
    }

    fn transfer_from(e: &Env, spender: Address, from: Address, to: Address, amount: i128) {
        Self::ContractType::transfer_from(e, &spender, &from, &to, amount);
    }

    fn approve(e: &Env, owner: Address, spender: Address, amount: i128, live_until_ledger: u32) {
        Self::ContractType::approve(e, &owner, &spender, amount, live_until_ledger);
    }

    fn decimals(e: &Env) -> u32 {
        Self::ContractType::decimals(e)
    }

    fn name(e: &Env) -> String {
        Self::ContractType::name(e)
    }

    fn symbol(e: &Env) -> String {
        Self::ContractType::symbol(e)
    }
}
```

## Explicación  del Contrato MyToken

### ¿Qué es este contrato?

Este es un **token fungible** (como una moneda digital) que funciona en la blockchain de Stellar. Piensa en él como crear tu propia criptomoneda, pero con reglas específicas que tú defines.

### Estructura General del Código

#### 1. Importaciones (Las herramientas que necesitamos)

```rust
use soroban_sdk::{contract, contractimpl, Address, Env, String};use stellar_fungible::{    capped::{check_cap, set_cap},    Base, FungibleToken,};
```

**¿Qué significa esto?**

* `soroban_sdk`: Es como la "caja de herramientas" básica para crear contratos en Stellar
* `stellar_fungible`: Son las funciones pre-construidas para crear tokens (como plantillas ya hechas)
* `capped`: Funciones para limitar la cantidad máxima de tokens que se pueden crear

#### 2. Definición del Contrato

```rust
#[contract]
pub struct Myt;
```

**Explicación simple:**

* `Myt` es el nombre de nuestro contrato (puedes cambiarlo por el que quieras)
* `#[contract]` le dice a Stellar "esto es un contrato inteligente"
* Es como crear una "fábrica" que va a producir tokens

### Partes Más Importantes

#### 🏗️ Constructor (La función que inicializa todo)

```rust
pub fn __constructor(e: &Env, cap: i128) {
   Base::set_metadata(e, 2, String::from_str(e, "MyToken"), String::from_str(e, "MYT"));
    set_cap(e, cap); 
}
```

**¿Qué hace?**

1. **`Base::set_metadata(...)`**: Define las características básicas del token:
   * 2: Decimales (como los centavos de las monedas)
   * `"MyToken"`: El nombre completo del token
   * `"MYT"`: El símbolo corto (como "USD" para dólares)
2. **`set_cap(e, cap)`**: Establece el límite máximo de tokens que se pueden crear
   * Ejemplo: Si pones `cap = 1000000`, nunca se podrán crear más de 1 millón de tokens

**Analogía**: Es como registrar una nueva moneda en el banco central, definiendo su nombre, símbolo y cuántas unidades máximo pueden existir.

#### 🪙 Función Mint (Crear nuevos tokens)

```rust
rustpub fn mint(e: &Env, account: Address, amount: i128) {    check_cap(e, amount);    Base::mint(e, &account, amount);}
```

**¿Qué hace?**

1. **`check_cap(e, amount)`**: Verifica que no se exceda el límite máximo
2. **`Base::mint(...)`**: Crea los tokens y los asigna a una cuenta específica

**Analogía**: Es como una máquina impresora de billetes, pero que primero verifica que no imprimas más de lo permitido.

#### 🔄 Implementación de FungibleToken (Las funciones estándar)

Esta parte implementa todas las funciones que cualquier token debe tener:

**Funciones de Consulta (Solo leen información):**

* **`total_supply()`**: ¿Cuántos tokens existen en total?
* **`balance()`**: ¿Cuántos tokens tiene una cuenta específica?
* **`decimals()`**: ¿Cuántos decimales tiene el token?
* **`name()`** y **`symbol()`**: ¿Cómo se llama el token?

**Funciones de Transferencia:**

* **`transfer()`**: Enviar tokens de una cuenta a otra
* **`transfer_from()`**: Permitir que alguien más mueva tus tokens (con permiso previo)
* **`approve()`**: Dar permiso a alguien para que use tus tokens
* **`allowance()`**: ¿Cuántos tokens puede usar alguien en mi nombre?

### ¿Por qué este diseño es inteligente?

#### 1. **Reutilización de código**

En lugar de escribir todas las funciones desde cero, usa `Base` que ya tiene todo implementado y probado.

#### 2. **Seguridad con límites**

La función `check_cap` asegura que nunca se puedan crear más tokens de los permitidos.

#### 3. **Estándar compatible**

Al implementar `FungibleToken`, tu token funciona con todas las aplicaciones que esperan tokens estándar.

### Compilación del contrato

Nos ubicamos dentro de ../stellar-contracts\examples\myt allí en consola ejecutamos:

```bash
cargo build --target wasm32-unknown-unknown --release
```

<figure><img src="../../.gitbook/assets/image (79).png" alt=""><figcaption><p>Resultado de una ejecución exitosa</p></figcaption></figure>

### Despliegue del contrato

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

```bash
stellar contract deploy *
  --wasm ../../target/wasm32-unknown-unknown/release/myt.wasm *
  --source developer *
  --network testnet *
  --alias MyToken  *
  -- *
  --cap 100000000
```

**Nota:**&#x50;ara un millón o cualquier cifra se ponen 2  ceros de más que son los centavos

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption><p>Despliegue exitoso</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption><p>Vista del contrato desplegado en testnet</p></figcaption></figure>

Acá podemos ver que se llama al constuctor del contrato y estamos poniendo que el máximo de tokens son 1 millón.

No obstante, a pesar que tenemos como máximo 1 millón, no hemos acuñado ninguna moneda.

A continuación vamos a invocar la función mint

```bash
stellar contract invoke *
--id <contract_id> *
--source developer *
--network testnet *
-- *
mint *
--account <tu_cuenta_destino> *
--amount 100000
```

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption><p>Resultado de la operación</p></figcaption></figure>

Para ver el  balance

```bash
stellar contract invoke *
--id <contract_id> *
--source developer *
--network testnet *
-- balance
--account <cuenta_a_verificar>
```

<figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption><p>Resultado de la operación</p></figcaption></figure>

Si queremos ver el  saldo de una forma visual en nuestra billetera 😃

Primero ejecutamos el siguiente comando

```bash
stellar keys secret <alias-de-cuenta>
```

En nuetos caso el alias de cuenta o entidad es **developer,** mandamos a descansar a Bob o Alice 😉\
\
Esto nos da la  llave secreta, esta llave secreta la añadimos en nuestra billetera favorita, en nuestro caso la billetera freigther

<figure><img src="../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

Una vez importada la billetera importamos el  contrato de token

<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

Al añadir el contrato del token vemos lo siguiente en nuestra billetera:

<figure><img src="../../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

⚠️ **OJO:** Si eres detallista, ves un agujero ENORME de seguridad, cualquiera puede hacer la operación de mint, este ejemplo sólo ha sido con fines ilustrativos 😅.

***

### Ejemplo 2: _Un token sencillo, pero solo el dueño puede acuñarlo_



_En proceso....._
