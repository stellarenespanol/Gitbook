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

