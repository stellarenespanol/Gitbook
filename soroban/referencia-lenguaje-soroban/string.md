---
description: Tipo de dato string que viene dentro de la libreria soroban
---

# String

Los strings pueden pasarse a contratos y almacenes utilizando el tipo Bytes.

Tenga en cuenta que los bytes contenidos en los strings no se ajustan necesariamente a ninguna codificación de texto estándar, como ASCII o Unicode UTF-8. Son bytes sin interpretar. Se trata de bytes sin interpretar, y los usuarios que esperen una codificación determinada deberán aplicarla manualmente.

Nos vamos a una ruta donde queramos crear nuestro proyecto y ponemos el siguiente comando:

```
stellar contract init  strings --name strings
```

Borramos el contrato generado en  lib.rs y ponemos el siguiente código

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, vec, Env, String, Vec};

#[contract]
pub struct Strings;

#[contractimpl]
impl Strings {
    // Crear un nuevo string
    pub fn create_string(env: Env, text: String) -> String {
        text
    }

    // Obtener la longitud de un string
    pub fn get_length(env: Env, text: String) -> u32 {
        text.len()
    }

    // Comparar dos strings
    pub fn compare_strings(env: Env, text1: String, text2: String) -> bool {
        text1 == text2
    }

    // detecta si un strig está vacio
    pub fn is_empty(env: Env, text1: String) -> bool {
        text1.is_empty()
    }
    /*está pendiente en las funcioalidades de  soroban_sdk::String la funcionalidad directa
    de apend o una cobre carga del operador +, por lo tanto usaremos un vector */
    pub fn concatenate(env: Env, text1: String, text2: String) -> Vec<String> {
        vec![&env, text1, text2]
    }
}
```

#### 📌 **Explicación general**

* Usa **#!\[no\_std]**, lo que indica que no usa la biblioteca estándar de Rust.
* Importa módulos de **soroban\_sdk**, necesarios para ejecutar el contrato en Soroban.
* Define un contrato llamado `Strings`.
* Implementa funciones para **crear, obtener la longitud, comparar, verificar si está vacío y concatenar strings**.
* Como **Soroban no admite concatenación directa de strings (`+`)**, se usa un `Vec<String>` para concatenación.

***

### 🛠 **Explicación de las funciones**

Todas las funciones reciben `env: Env`, que es necesario para la ejecución en Soroban.

#### 1️⃣ `create_string(env: Env, text: String) -> String`

✅ **Crea un string y lo devuelve.**

📌 **Paso a paso:**

1. Recibe un string como entrada.
2. Simplemente lo retorna.

📌 **Ejemplo:**\
Si llamamos `create_string("Hola")`, devuelve `"Hola"`.

***

#### 2️⃣ `get_length(env: Env, text: String) -> u32`

✅ **Obtiene la cantidad de caracteres en un string.**

📌 **Paso a paso:**

1. Recibe un string como entrada.
2. Usa `.len()` para obtener su longitud.
3. Retorna la cantidad de caracteres.

📌 **Ejemplo:**\
Si llamamos `get_length("Hola")`, devuelve `4`.

***

#### 3️⃣ `compare_strings(env: Env, text1: String, text2: String) -> bool`

✅ **Compara si dos strings son iguales.**

📌 **Paso a paso:**

1. Recibe dos strings como entrada.
2. Compara ambos usando `==`.
3. Retorna `true` si son iguales, `false` si no.

📌 **Ejemplo:**\
Si llamamos `compare_strings("Hola", "Hola")`, devuelve `true`.\
Si llamamos `compare_strings("Hola", "Mundo")`, devuelve `false`.

***

#### 4️⃣ `is_empty(env: Env, text1: String) -> bool`

✅ **Verifica si un string está vacío.**

📌 **Paso a paso:**

1. Recibe un string como entrada.
2. Usa `.is_empty()` para verificar si tiene contenido.
3. Retorna `true` si el string está vacío, `false` si tiene caracteres.

📌 **Ejemplo:**\
Si llamamos `is_empty("")`, devuelve `true`.\
Si llamamos `is_empty("Hola")`, devuelve `false`.

***

#### 5️⃣ `concatenate(env: Env, text1: String, text2: String) -> Vec<String>`

✅ **Une dos strings en un vector.**

📌 **Paso a paso:**

1. Recibe dos strings como entrada.
2. Crea un `Vec<String>` con ambos valores.
3. Retorna el vector.

📌 **Ejemplo:**\
Si llamamos `concatenate("Hola", "Mundo")`, devuelve `["Hola", "Mundo"]`.

🔹 **Nota:**\
Soroban aún **no** soporta concatenación directa con `+`, por lo que se usa un vector para unir los valores.

### 📌 **Resumen**

Este contrato proporciona funciones útiles para manipular strings en Soroban:

* `create_string()`: Retorna el string recibido.
* `get_length()`: Retorna la longitud del string.
* `compare_strings()`: Compara si dos strings son iguales.
* `is_empty()`: Verifica si un string está vacío.
* `concatenate()`: Une dos strings en un vector (ya que la concatenación directa no está disponible en Soroban).

Este contrato permite manejar textos dentro de la blockchain de manera eficiente. 🚀



**Compilación del contrato**

Ejecutamos lo siguiente:

```
Stellar contract build
```

**Despliegue del contrato**

Para Mac y Linux el salto de línea es con el carácter " **\\**" y en Windows con el carácter " **´** "

Reemplaze el simbolo \* por el respectivo carácter de salto de linea a su sistema operativo.

```
stellar contract deploy *
  --wasm target/wasm32-unknown-unknown/release/strings.wasm *
  --source developer *
  --network testnet *
  --alias strings
```

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Pruebas del contrato**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

**Función`create_string`**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
create_string *
--text "Hello Soroban"
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función** get\_length

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
get_length *
--text "Hello Soroban"
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función** `compare_strings`

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
compare_strings *
--text1 "Hello Soroban" *
--text2 "Hello Stellar" *
```

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption><p>ejecución de prueba</p></figcaption></figure>

**Función `is_empty`**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
is_empty *
--text1 "Algún texto" 
```

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función `concatenate`**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
concatenate *
--text1 "Algún texto" *
--text2 "Algún otro texto" 
```

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption><p>Ejecuciónd e prueba</p></figcaption></figure>
