# Colecciones

### 📚 Colecciones

Las colecciones son estructuras de datos que te permiten almacenar y manipular múltiples valores. En Soroban, están optimizadas para el entorno blockchain y difieren de las implementaciones estándar de Rust.

* **Vec (soroban\_sdk::vec::Vec)**: Implementación propia de Soroban para vectores dinámicos.
* **Map (soroban\_sdk::map::Map)**: Implementación propia de Soroban para colecciones de pares clave-valor.

#### 1. Vec en Soroban 🌟

El **Vec** en Soroban es una colección dinámica que permite almacenar una secuencia de elementos de un mismo tipo. Al igual que en Rust, su tamaño puede crecer de forma dinámica. En el contexto de Soroban se trabaja con él a través del entorno (env), lo que facilita su integración en contratos inteligentes.

```rust
use soroban_sdk::{Env, Vec};

pub fn ejemplo_vec(env: &Env) {
    let mut numeros = Vec::new(env); // Crea un Vec vacío
    numeros.push(10);
    numeros.push(20);
    // Puedes registrar el contenido usando el log del entorno
    env.log().info("Contenido del Vec:", numeros);
}
```

***

#### 2. Map en Soroban 🔄

El **Map** en Soroban es una colección de pares clave-valor, muy útil para asociar claves únicas a valores, similar a un diccionario. También requiere el entorno (env) para su creación y manipulación, lo que lo hace ideal para el manejo de datos en contratos inteligentes.

```rust
use soroban_sdk::{Env, Map};

pub fn ejemplo_map(env: &Env) {
    let mut mi_mapa = Map::new(env); // Crea un Map vacío
    mi_mapa.set(1, "uno");
    mi_mapa.set(2, "dos");
    // Registra el contenido del Map
    env.log().info("Contenido del Map:", mi_mapa);
}
```

**Contratos inteligentes ejemplo:**

Abrimos la consola en la ruta donde deseamos crear el proyecto y ejecutamos.

```bash
stellar contract init collections --name vecmap
```

Borramos todo el código y ponemos lo siguiente:

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, symbol_short, Env, Map, String, Symbol, Vec};

#[contract]
pub struct VecMapContract;

#[contractimpl]
impl VecMapContract {
    const VEC_KEY: Symbol = symbol_short!("vec");
    const MAP_KEY: Symbol = symbol_short!("map");

    fn get_vec(env: &Env) -> Vec<String> {
        env.storage()
            .instance()
            .get(&Self::VEC_KEY)
            .unwrap_or(Vec::new(&env))
    }

    fn set_vec(env: &Env, vec: &Vec<String>) {
        env.storage().instance().set(&Self::VEC_KEY, vec);
    }

    // Funciones para manejo de Vec
    pub fn add_vec(env: Env, value: String) -> u32 {
        let mut my_vec: Vec<String> = Self::get_vec(&env);
        my_vec.push_back(value);
        Self::set_vec(&env, &my_vec);
        my_vec.len()
    }

    //Obtenemos un elemento del vector
    pub fn get_vec_element(env: Env, index: u32) -> Option<String> {
        let my_vec: Vec<String> = Self::get_vec(&env);
        my_vec.get(index)
    }

    pub fn remove_vec_element(env: Env, index: u32) -> Option<()> {
        let mut my_vec = Self::get_vec(&env);
        // Verificamos primero si el índice es válido
        if my_vec.get(index).is_some() {
            let element: Option<()> = my_vec.remove(index);
            Self::set_vec(&env, &my_vec);
            Some(())
        } else {
            None
        }
    }

    fn get_map(env: &Env) -> Map<Symbol, String> {
        env.storage()
            .instance()
            .get(&Self::MAP_KEY)
            .unwrap_or(Map::new(env))
    }

    fn set_map(env: &Env, map: &Map<Symbol, String>) {
        env.storage().instance().set(&Self::MAP_KEY, map);
    }

    // Funciones para manejo de Map
    
    // Agregamos un elemento al map
    pub fn add_map_entry(env: Env, key: Symbol, value: String) {
        let mut my_map = Self::get_map(&env);
        my_map.set(key, value);
        Self::set_map(&env, &my_map);
    }
    //Obtener algun valor del map
    pub fn get_map_value(env: Env, key: Symbol) -> Option<String> {
        let my_map = Self::get_map(&env);
        my_map.get(key)
    }

    // Eliminar un elemento del map
    pub fn remove_map_entry(env: Env, key: Symbol) -> Option<()> {
        let env_ref = &env;
        let mut my_map = Self::get_map(env_ref);
        if my_map.remove(key).is_some() {
            Self::set_map(&env, &my_map);
            Some(()) // Éxito
        } else {
            None     // La clave no existía
        }
    }
}
```

## Explicación del contrato

📌 **Estructuras y Tipos del Contrato**

**Atributo `#![no_std]`**\
Indica que el contrato no utiliza la biblioteca estándar de Rust, lo cual es común en entornos embebidos o en contratos inteligentes para reducir dependencias y adaptarse a restricciones de recursos.

**Macros de Contrato**\
`#[contract]` y `#[contractimpl]`\
Estas macros son proporcionadas por el _soroban\_sdk_ y se usan para marcar:

* **`#[contract]`**: Define la estructura principal del contrato.
* **`#[contractimpl]`**: Implementa la lógica de negocio del contrato.\
  Para más detalles, consulta la documentación de contratos Soroban en:\
  [Escribir Contratos en Soroban](https://soroban.stellar.org/docs/developers/writing-contracts)

**Constantes del Contrato**

* **`VEC_KEY`**: Clave (tipo `Symbol`) para almacenar y recuperar el vector (`Vec<String>`) en el storage.
* **`MAP_KEY`**: Clave (tipo `Symbol`) para almacenar y recuperar el mapa (`Map<Symbol, String>`) en el storage.

***

🛠 **Funciones del Contrato**

1️⃣ **get\_vec**

```rust
fn get_vec(env: &Env) -> Vec<String> {
    env.storage()
        .instance()
        .get(&Self::VEC_KEY)
        .unwrap_or(Vec::new(&env))
}
```

**Descripción:**\
Recupera el vector almacenado en el contrato. Si no existe, devuelve un vector vacío.

**Mecanismo:**

* Accede al storage utilizando la clave `VEC_KEY`.
* Utiliza `unwrap_or` para devolver un `Vec` nuevo en caso de que no se encuentre ningún valor.

***

2️⃣ **set\_vec**

```rust
fn set_vec(env: &Env, vec: &Vec<String>) {
    env.storage().instance().set(&Self::VEC_KEY, vec);
}
```

**Descripción:**\
Almacena el vector proporcionado en el storage del contrato, usando la clave `VEC_KEY`.

**Mecanismo:**

* Se guarda el `Vec` de cadenas en el storage para persistir los cambios.

***

3️⃣ **add\_vec**

```rust
pub fn add_vec(env: Env, value: String) -> u32 {
    let mut my_vec: Vec<String> = Self::get_vec(&env);
    my_vec.push_back(value);
    Self::set_vec(&env, &my_vec);
    my_vec.len()
}
```

**Descripción:**\
Agrega un elemento al final del vector y devuelve la nueva longitud del mismo.

**Mecanismo:**

* Recupera el vector actual mediante `get_vec`.
* Usa `push_back` para agregar el valor (tipo `String`).
* Actualiza el storage con el vector modificado mediante `set_vec`.
* Retorna la longitud del vector actualizado.

***

4️⃣ **get\_vec\_element**

```rust
pub fn get_vec_element(env: Env, index: u32) -> Option<String> {
    let my_vec: Vec<String> = Self::get_vec(&env);
    my_vec.get(index)
}
```

**Descripción:**\
Obtiene el elemento ubicado en el índice especificado del vector.\
Devuelve `Some(elemento)` si existe o `None` si el índice es inválido.

**Mecanismo:**

* Recupera el vector desde el storage.
* Usa el método `get` para obtener el elemento en la posición indicada.

***

5️⃣ **remove\_vec\_element**

```rust
pub fn remove_vec_element(env: Env, index: u32) -> Option<()> {
    let mut my_vec = Self::get_vec(&env);
    if my_vec.get(index).is_some() {
        let element: Option<()> = my_vec.remove(index);
        Self::set_vec(&env, &my_vec);
        Some(())
    } else {
        None
    }
}
```

**Descripción:**\
Elimina el elemento en el índice indicado del vector.\
Retorna `Some(())` si la eliminación fue exitosa o `None` si el índice no es válido.

**Mecanismo:**

* Verifica si el índice es válido utilizando `get`.
* Si existe el elemento, se elimina mediante `remove`.
* Se actualiza el vector en el storage usando `set_vec`.

***

6️⃣ **get\_map**

```rust
fn get_map(env: &Env) -> Map<Symbol, String> {
    env.storage()
        .instance()
        .get(&Self::MAP_KEY)
        .unwrap_or(Map::new(env))
}
```

**Descripción:**\
Recupera el mapa almacenado en el contrato.\
Si no existe, devuelve un mapa nuevo y vacío.

**Mecanismo:**

* Accede al storage usando la clave `MAP_KEY`.
* Utiliza `unwrap_or` para retornar un `Map` vacío si no se encuentra ningún valor.

***

7️⃣ **set\_map**

```rust
fn set_map(env: &Env, map: &Map<Symbol, String>) {
    env.storage().instance().set(&Self::MAP_KEY, map);
}
```

**Descripción:**\
Almacena el mapa proporcionado en el storage del contrato usando la clave `MAP_KEY`.

**Mecanismo:**

* Se guarda el `Map` en el storage para persistir los cambios.

***

8️⃣ **add\_map\_entry**

```rust
pub fn add_map_entry(env: Env, key: Symbol, value: String) {
    let mut my_map = Self::get_map(&env);
    my_map.set(key, value);
    Self::set_map(&env, &my_map);
}
```

**Descripción:**\
Agrega una entrada al mapa, utilizando una clave (`Symbol`) y su valor asociado (`String`).

**Mecanismo:**

* Recupera el mapa actual desde el storage.
* Utiliza el método `set` del `Map` para agregar la nueva entrada.
* Actualiza el storage con el mapa modificado.

***

9️⃣ **get\_map\_value**

```rust
pub fn get_map_value(env: Env, key: Symbol) -> Option<String> {
    let my_map = Self::get_map(&env);
    my_map.get(key)
}
```

**Descripción:**\
Obtiene el valor asociado a la clave especificada en el mapa.\
Retorna `Some(valor)` si existe o `None` si la clave no se encuentra.

**Mecanismo:**

* Recupera el mapa desde el storage.
* Utiliza `get` para buscar el valor asociado a la clave dada.

***

🔟 **remove\_map\_entry**

```rust
pub fn remove_map_entry(env: Env, key: Symbol) -> Option<()> {
    let env_ref = &env;
    let mut my_map = Self::get_map(env_ref);
    if my_map.remove(key).is_some() {
        Self::set_map(&env, &my_map);
        Some(())
    } else {
        None
    }
}
```

**Descripción:**\
Elimina la entrada del mapa correspondiente a la clave especificada.\
Retorna `Some(())` si la operación fue exitosa o `None` si la clave no existía en el mapa.

**Mecanismo:**

* Recupera el mapa actual desde el storage.
* Utiliza el método `remove` para eliminar la entrada asociada a la clave.
* Si se elimina, actualiza el storage con el mapa modificado mediante `set_map`.

***

📌 **Resumen General**\
Este contrato inteligente demuestra cómo manejar estructuras de datos dinámicas en Soroban utilizando `Vec` y `Map` para gestionar colecciones de elementos de manera persistente en el storage.\
Permite:

* **Manipulación de Vectores (Vec):**\
  Agregar, obtener y eliminar elementos del vector almacenado.
* **Manipulación de Mapas (Map):**\
  Agregar, recuperar y eliminar entradas del mapa almacenado.

**Compilación del contrato**

Ejecutamos lo siguiente:

```bash
Stellar contract build
```

**Despliegue del contrato**

Para Mac y Linux el salto de línea es con el carácter " **\\**" y en Windows con el carácter " **´** "

Reemplaze el simbolo \* por el respectivo carácter de salto de linea a su sistema operativo.

```bash
stellar contract deploy *
  --wasm target/wasm32-unknown-unknown/release/vecmap.wasm *
  --source <Identity> *
  --network testnet *
  --alias option
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Pruebas del contrato**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

**Función add\_vec:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
add_vec *
--value "Hello 🌎"
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función get\_vec\_element:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
get_vec_element *
--index 0
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función remove\_vec\_element:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
remove_vec_element *
--index 0
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función add\_map\_entry:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
add_map_entry *
--key "key1" *
--value "hola que más?"
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función get\_map\_value:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
get_map_value *
--key "key1"
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función remove\_map\_entry:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
remove_map_entry *
--key "key1"
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>
