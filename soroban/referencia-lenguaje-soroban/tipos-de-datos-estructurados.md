# Tipos de Datos Estructurados

### 🧱 Tipos de Datos Estructurados

Los tipos de datos estructurados son construcciones que te permiten organizar y agrupar datos relacionados bajo una misma entidad. Son fundamentales para modelar conceptos complejos en tus contratos inteligentes.

* **Structs**: Estructuras que agrupan campos con nombre y tipos diferentes.
* **Enums**: Tipos que pueden contener uno de varios valores posibles predefinidos.
* **Tuplas**: Colecciones ordenadas y de tamaño fijo que pueden contener elementos de diferentes tipos.
* **Arrays**: Colecciones de elementos del mismo tipo con longitud fija.

#### 1. Structs 🏗️

Las **structs** en Rust te permiten agrupar campos con nombres y tipos diferentes. Es como crear tu propio "molde" para agrupar datos relacionados.

```rust
rustCopiarEditarstruct Persona {
    nombre: String,
    edad: u32,
}

fn main() {
    let persona = Persona {
        nombre: String::from("Juan"),
        edad: 30,
    };
    println!("{} tiene {} años 👤", persona.nombre, persona.edad);
}
```

***

#### 2. Enums 🎨

Los **enums** definen un tipo que puede ser uno de varios valores predefinidos. Son muy útiles para representar estados o variantes.

```rust
rustCopiarEditarenum Estado {
    Activo,
    Inactivo,
    Desconocido,
}

fn main() {
    let estado = Estado::Activo;
    match estado {
        Estado::Activo => println!("¡Está activo! 😊"),
        Estado::Inactivo => println!("Está inactivo. 😴"),
        Estado::Desconocido => println!("Estado desconocido. 🤔"),
    }
}
```

***

#### 3. Tuplas 🔗

Las **tuplas** son colecciones ordenadas de tamaño fijo que pueden contener elementos de diferentes tipos. ¡Son perfectas para agrupar datos relacionados sin necesidad de nombres!

```rust
rustCopiarEditarfn main() {
    let persona: (&str, u32) = ("Ana", 25);
    println!("{} tiene {} años 👩", persona.0, persona.1);
}
```

***

#### 4. Arrays 📚

Los **arrays** son colecciones de elementos del mismo tipo con una longitud fija. Se usan cuando conoces la cantidad exacta de elementos y estos son del mismo tipo.

```rust
rustCopiarEditarfn main() {
    let numeros: [i32; 5] = [1, 2, 3, 4, 5];
    println!("El primer número es: {} ", numeros[0]);
}
```

**Contratos inteligentes ejemplo:**

En esta ocasión vamos a crear un contrato independiente por cada tipo de dato de la siguiente manera.

Abrimos la consola en la ruta donde deseamos crear el proyecto y ejecutamos.

```bash
stellar contract init structured_data_types --name structured_data_types
```

&#x20;Borramos todo el código y ponemos lo siguiente:

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, contracttype, Address, Env, String, };

#[contracttype]
#[derive(Clone, Debug, Eq, PartialEq)]
pub enum TaskStatus {
    Open = 0,
    InProgress = 1,
    Completed = 2,
}

#[contracttype]
#[derive(Clone, Debug, Eq, PartialEq)]
pub struct Task {
    pub id: u32,
    pub description: String,
    pub status: TaskStatus,
    pub assignee: Address,
}

#[contract]
pub struct SimpleContract;
#[contractimpl]
impl SimpleContract {
    // Función que busca una fruta en un array y devuelve su posición o -1 si no la encuentra.
    pub fn find_fruit(env: Env,fruit: String) -> i32 {
        let fruits: [String; 5] = [
            String::from_str(&env,"manzana"),
            String::from_str(&env,"banana"),
            String::from_str(&env,"naranja"),
            String::from_str(&env,"uva"),
            String::from_str(&env,"fresa"),
        ];

        for (index, f) in fruits.iter().enumerate() {
            if f == &fruit {
                // Compara la fruta dada con cada elemento del array.
                return index as i32; // Devuelve la posición (índice) si la encuentra.
            }
        }

        -1 // Devuelve -1 si la fruta no está en el array.
    }

 
    // Función que crea una tarea y devuelve el struct
   pub fn create_task(env: Env, id: u32, description: String, assignee: Address) -> Task {
        Task {
            id,
            description,
            status: TaskStatus::Open,
            assignee,
        }
    }
 
     // Función que devuelve una tupla con información
    pub fn get_info(env: Env) -> (String, i32) {
        (String::from_str(&env,"Ejemplo Simple"), 123)
    }

    // Función que devuelve la descripción del estado de la tarea
    pub fn get_status_description(env: Env,status: TaskStatus) -> String {
        match status {
            TaskStatus::Open => String::from_str(&env,"Abierta"),
            TaskStatus::InProgress => String::from_str(&env,"En Progreso"),
            TaskStatus::Completed => String::from_str(&env,"Completada"),
        }
    }
}
```



### Explicación del contrato

### 📌 **Estructuras y Tipos del Contrato**

#### 1. **Atributo `#![no_std]`**

* Indica que el contrato no utiliza la biblioteca estándar de Rust, lo que es común en entornos embebidos o en contratos inteligentes para reducir dependencias y adaptarse a restricciones de recursos.

#### 2. **Macros de Contrato**

* **`#[contract]`**, **`#[contractimpl]`** y **`#[contracttype]`**\
  Estas macros son proporcionadas por el _soroban\_sdk_ y se usan para marcar:
  * **`#[contract]`**: Define la estructura principal del contrato.
  * **`#[contractimpl]`**: Implementa la lógica de negocio del contrato.
  * **`#[contracttype]`**: Declara tipos (enums o structs) que se utilizarán en la interfaz del contrato.\
    [Para más detalles, consulta la documentación de contratos Soroban.](https://developers.stellar.org/docs/build/smart-contracts/example-contracts/custom-types#how-it-works)

#### 3. **Enum `TaskStatus`**

* Define los posibles estados de una tarea:
  * `Open` (valor 0)
  * `InProgress` (valor 1)
  * `Completed` (valor 2)
* Se deriva de `Clone`, `Debug`, `Eq` y `PartialEq` para facilitar la clonación, la depuración y la comparación de valores.

#### 4. **Struct `Task`**

* Representa una tarea y contiene:
  * `id: u32`: Identificador único de la tarea.
  * `description: String`: Descripción de la tarea.
  * `status: TaskStatus`: Estado actual de la tarea.
  * `assignee: Address`: Dirección del usuario asignado a la tarea.
* **`Address`** es un tipo que representa direcciones de cuentas o contratos en Soroban.\
  [Más información en la documentación de Address.](https://developers.stellar.org/docs/learn/encyclopedia/contract-development/types/built-in-types#address-address)

#### 5. **Tipos del SDK**

* **`Env`**: Proporciona el entorno de ejecución del contrato, permitiendo el acceso a almacenamiento, logging y otras funciones.\
  [Consulta Env en la documentación de Soroban.](https://developers.stellar.org/docs/learn/encyclopedia/contract-development/environment-concepts#values-and-types)
* **`String`**: Tipo de cadena adaptado a entornos _no\_std_ que se utiliza para manejar textos en el contrato.\
  [Más información en String en Soroban.](https://developers.stellar.org/docs/learn/encyclopedia/contract-development/types/built-in-types#bytes-strings-bytes-bytesn-string)

***

### 🛠 **Funciones del Contrato**

#### 1. **find\_fruit**

```rust
pub fn find_fruit(env: Env, fruit: String) -> i32 {
    let fruits: [String; 5] = [
        String::from_str(&env,"manzana"),
        String::from_str(&env,"banana"),
        String::from_str(&env,"naranja"),
        String::from_str(&env,"uva"),
        String::from_str(&env,"fresa"),
    ];

    for (index, f) in fruits.iter().enumerate() {
        if f == &fruit {
            // Si encuentra la fruta, retorna el índice convertido a i32.
            return index as i32;
        }
    }
    // Si no se encuentra, retorna -1.
    -1
}
```

* **Descripción:**\
  Busca en un arreglo de 5 frutas (como cadenas) la que coincida con la cadena de entrada `fruit` y devuelve su posición (índice).
* **Mecanismo:**
  * Se crea un arreglo estático de frutas.
  * Se recorre el arreglo utilizando `enumerate()`, que proporciona el índice y el elemento.
  * Si se encuentra una coincidencia (usando `if`), se retorna el índice.
  * Si no hay coincidencia, se retorna `-1`.

#### 2. **create\_task**

```rust
pub fn create_task(env: Env, id: u32, description: String, assignee: Address) -> Task {
    Task {
        id,
        description,
        status: TaskStatus::Open,
        assignee,
    }
}
```

* **Descripción:**\
  Crea una nueva tarea con un identificador, descripción y usuario asignado. El estado inicial de la tarea se establece en `TaskStatus::Open`.
* **Mecanismo:**
  * Se construye una instancia de la estructura `Task` con los valores proporcionados.
  * Se asigna el estado inicial de la tarea como _abierta_.

#### 3. **get\_info**

```rust
pub fn get_info(env: Env) -> (String, i32) {
    (String::from_str(&env,"Ejemplo Simple"), 123)
}
```

* **Descripción:**\
  Devuelve una tupla que contiene un mensaje y un número entero.
* **Mecanismo:**
  * Se crea una cadena `"Ejemplo Simple"`.
  * Se retorna junto a un entero fijo `123`.

#### 4. **get\_status\_description**

```rust
rustCopiarEditarpub fn get_status_description(env: Env, status: TaskStatus) -> String {
    match status {
        TaskStatus::Open => String::from_str(&env,"Abierta"),
        TaskStatus::InProgress => String::from_str(&env,"En Progreso"),
        TaskStatus::Completed => String::from_str(&env,"Completada"),
    }
}
```

* **Descripción:**\
  Devuelve una cadena descriptiva según el estado de una tarea.
* **Mecanismo:**
  * Se utiliza la estructura de control **`match`** para evaluar el valor del `TaskStatus`.
  * Según el caso (Open, InProgress, Completed), se retorna la cadena correspondiente: `"Abierta"`, `"En Progreso"` o `"Completada"`.
* **Contexto Teórico del `match`:**\
  En Rust, **`match`** es similar a la instrucción `switch` de otros lenguajes, pero es más poderoso, permitiendo comparar contra patrones y asegurando que todos los casos sean tratados o manejados mediante un caso por defecto. Esto proporciona una forma segura y expresiva de controlar el flujo del programa.

***

### 📌 **Resumen General**

Este contrato inteligente demuestra:

* **Definición de Tipos Personalizados:**\
  Con el enum `TaskStatus` y la estructura `Task`, se modelan estados y tareas para un posible sistema de gestión.
* **Búsqueda en Arreglos:**\
  La función `find_fruit` muestra cómo recorrer un arreglo de cadenas y buscar un elemento.
* **Creación y Manejo de Estructuras:**\
  La función `create_task` crea una tarea inicializada con un estado predeterminado.
* **Uso de Tuplas y `match`:**\
  La función `get_info` devuelve información empaquetada en una tupla y `get_status_description` utiliza `match` para retornar descripciones basadas en el estado.

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
  --wasm target/wasm32-unknown-unknown/release/structured_data_types.wasm *
  --source developer *
  --network <Identity> *
  --alias structured_data_types
```

<figure><img src="../../.gitbook/assets/image (58).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Pruebas del contrato**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

**función&#x20;**_**find\_fruit**_

```bash
stellar contract invoke `
--id <CONTRACT_ID> *
--source <Identity> `
--network testnet `
-- <Identity>`
find_fruit `
--fruit "manzana"
```

<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Función&#x20;**_**create\_task**_

```bash
stellar contract invoke *
  --id <CONTRACT_ID> *
  --source<Identity> *
  --network testnet *
  -- *
  create_task *
--id 1 *
--description "Tarea de ejemplo" *
--assignee <Stellar address>"
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption><p>Ejecución de ejemplo</p></figcaption></figure>

**Función&#x20;**_**get\_info**_

```bash
stellar contract invoke `
  --id <CONTRACT_ID> *
  --source <identity> `
  --network testnet `
  -- `
  get_info
```

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

Función _get\_status\_description_

```bash
stellar contract invoke `
  --id <CONTRACT_ID> `
  --source <identity> `
  --network testnet `
  --  `
get_status_description  `
--status 0
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>
