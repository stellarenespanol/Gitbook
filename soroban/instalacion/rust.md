---
description: Instalación de Rust en Mac/Linux y Windows
---

# 🦀 Rust

Rust es el lenguaje de propósito general en el cual se escriben los contratos para la red de Stellar a continuación un paso a paso para su instalación.

Para la instalación de rust, dirigirse a este enlace:\
[https://www.rust-lang.org/es/tools/install](https://www.rust-lang.org/es/tools/install)

### **Instalación en macOS y Linux**

#### Paso 1: Abrir la Terminal

Abre tu aplicación de terminal (Terminal.app en macOS o la terminal de tu distribución Linux).

#### Paso 2: Descargar e instalar Rust con rustup

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

_Este comando descargará un script y te guiará por el proceso de instalación. Generalmente se recomienda usar la opción predeterminada para instalar la versión estable._

#### Paso 3: Configurar el entorno

Una vez finalizada la instalación, cierra y vuelve a abrir la terminal o ejecuta:

```bash
source $HOME/.cargo/env
```

Esto garantiza que el directorio de Cargo (donde se instalan las herramientas, por ejemplo, `rustc`, `cargo` y `rustup`) se añada a tu variable de entorno `PATH`.

#### Paso 4: Verificar la instalación

Comprueba que Rust se instaló correctamente ejecutando:

```bash
rustc --version
```

#### Paso 5: Instalar un compilador de C (si es necesario)

Rust requiere un enlazador para compilar correctamente:

* **En macOS:** Instala las herramientas de línea de comandos de Xcode

```bash
xcode-select --install
```

* **En Linux (por ejemplo, Ubuntu):** Asegúrate de tener instalado el paquete `build-essential`

```bash
sudo apt-get update
sudo apt-get install build-essential
```

* Fedora

```bash
sudo dnf update
sudo dnf install make automake gcc gcc-c++ kernel-devel
```

* Arch

```bash
sudo pacman -Syu
sudo pacman -S base-devel
```

#### Paso 6: Agregar lo siguiente a Rust, para poder generar archivos WebAssembly

Ejecutamos lo siguente para saver laversión de Rust:

```bash
rustc --version
```

_Si el rust es anterior a la versión 1.85_

```bash
rustup target add wasm32-unknown-unknown 
```

_Si el rust es igual o mayor a la versión 1.85_

```bash
rustup target add wasm32v1-none
```

### Instalación en Windows

#### Paso 1: Descargar el instalador

Visita la [página oficial de Rust](https://www.rust-lang.org/es/tools/install) y descarga el instalador para Windows (rustup-init.exe). Elige la versión de 32 o 64 bits según tu sistema.

#### Paso 2: Ejecutar el instalador

Ejecuta el archivo descargado y sigue las instrucciones en pantalla. Durante la instalación, es posible que se te pida instalar las **Visual Studio C++ Build Tools** (necesarias para compilar Rust en Windows). También debemos instalar el windows SDK acorde a la versión de windows que tengamos.

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Windows 11 SDK si tenemos Windows 11, Windows 10 SDK, si tenemos la versión 10 de Windows</p></figcaption></figure>

#### Paso 3: Verificar la instalación

Abre una terminal (CMD o PowerShell) y ejecuta:

```powershell
rustc --version
```

#### Paso 4: Agregamos los siguientes paquetes



```powershell
rustup toolchain install stable-x86_64-pc-windows-gnu
rustup default stable-x86_64-pc-windows-gnu

```

_Si el rust es anterior a la versión 1.85_

Ejecutamos lo siguente para saver laversión de Rust:

```bash
rustc --version
```

```bash
rustup target add wasm32-unknown-unknown 
```

_Si el rust es igual o mayor a la versión 1.85_

```bash
rustup target add wasm32v1-none
```

```
```

