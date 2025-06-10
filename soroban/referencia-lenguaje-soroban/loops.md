# Loops

📚 Loops en Rust: Repetición de código de forma eficiente

En programación, los loops (o bucles) permiten repetir la ejecución de un bloque de código automáticamente. Son útiles para realizar tareas repetitivas, recorrer colecciones de datos o ejecutar acciones hasta que se cumpla una condición.

⚠️ En contratos inteligentes, es importante que los loops tengan límites claros para evitar ciclos infinitos y un consumo excesivo de gas.

✅ loop (bucle infinito controlado) Se ejecuta indefinidamente hasta que se encuentra una condición de salida.

📌 Sintaxis básica:

```rust
loop {
    // Código a ejecutar en cada iteración
    if condicion {
        break; // Termina el loop
    }
}
```

📌 Ejemplo en Rust:

```rust
fn main() {
    let mut count = 0;
    loop {
        println!("Contador: {} 😄", count);
        count += 1;
        if count == 5 {
            break;
        }
    }
}
```

📌 Cuándo usar loop Cuando no se sabe cuántas iteraciones se necesitarán y se desea un control manual de salida.

✅ while (bucle condicional) Ejecuta el bloque de código mientras la condición sea verdadera.

📌 Sintaxis básica:

```
while condicion {
    // Código a ejecutar mientras la condición sea verdadera
}
```

📌 Ejemplo en Rust:

```
fn main() {
    let mut count = 0;
    while count < 5 {
        println!("Contador: {} 🚀", count);
        count += 1;
    }
}
```

📌 Cuándo usar while Cuando no se conoce el número exacto de iteraciones, pero se tiene una condición de parada.

✅ for (bucle iterativo) Recorre colecciones de datos de manera segura y controlada.

📌 Sintaxis básica:

```rust
for elemento in coleccion.iter() {
    // Código que usa cada elemento
}
```

📌 Ejemplo con array:

```rust
fn main() {
    let numeros = [1, 2, 3, 4, 5];
    for numero in numeros.iter() {
        println!("Número: {} 💡", numero);
    }
}
```

📌 Ejemplo con vector:

```rust
fn main() {
    let elementos = vec!["a", "b", "c"];
    for elemento in elementos.iter() {
        println!("Elemento: {} 🔥", elemento);
    }
}
```

📌 Cuándo usar for Cuando se necesita iterar sobre una colección con un número finito de elementos.

⚡ El bucle for es el más recomendado en contratos inteligentes, ya que garantiza que el número de iteraciones es limitado.

***

### Código en Soroban

Nos vamos a una ruta donde queramos crear nuestro proyecto y ponemos el siguiente comando:

```
stellar contract init  loops --name loops
```

Borramos el contrato generado en  lib.rs y ponemos el siguiente código

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl,  Env, String, Vec};

#[contract]
pub struct LoopContract;

#[contractimpl]
impl LoopContract {
    
    pub fn ejemplo_loop(env: Env) -> Vec<u32> {
        let mut count = 0;
        let mut result:Vec<u32> = Vec::new(&env);
        loop {
            result.push_back(  count);
            count += 1;
            if count == 5 {
                break;
            }
        }
        result
    }

    pub fn ejemplo_while(env: Env) -> Vec<u32> {
        let mut count = 0;
        let mut result:Vec<u32> = Vec::new(&env);
        while count < 5 {
            result.push_back(count);
            count += 1;
        }
        result
    }

    pub fn ejemplo_for(env: Env) -> Vec<String> {
      
        let elementos: [&str; 3] = ["a", "b", "c"];
        let mut result = Vec::new(&env);
        for elemento in elementos.iter() {
            result.push_back(String::from_str( &env, elemento));
        }
        result
    }
}
```

📌 **Descripción General del Contrato**\


Este contrato inteligente, denominado **LoopContract**, está desarrollado en Rust para la plataforma Soroban. Utiliza la directiva `#![no_std]` y módulos de `soroban_sdk` para operar sin la biblioteca estándar de Rust.&#x20;

El contrato define tres funciones principales: **ejemplo\_loop**, **ejemplo\_while** y **ejemplo\_for**, cada una demostrando diferentes estructuras de control de flujo para iteración en Rust.​

🛠 **Explicación de las Funciones**

1️⃣ **ejemplo\_loop**

```rust
rustCopiarEditarpub fn ejemplo_loop(env: Env) -> Vec<u32> {
    let mut count = 0;
    let mut result: Vec<u32> = Vec::new(&env);
    loop {
        result.push_back(count);
        count += 1;
        if count == 5 {
            break;
        }
    }
    result
}
```

**Descripción:**\
Genera una lista de números enteros desde 0 hasta 4 utilizando un bucle infinito controlado por una condición interna.​

**Mecanismo:**

* Inicializa la variable `count` en 0 y crea un vector vacío `result` para almacenar los resultados.​
* Utiliza un bucle `loop` que se ejecuta indefinidamente hasta que se cumpla una condición de salida.​
* En cada iteración, añade el valor actual de `count` al final del vector `result` usando `push_back`.​
* Incrementa `count` en 1.​
* Si `count` alcanza el valor de 5, se ejecuta la sentencia `break` para salir del bucle.​
* Finalmente, retorna el vector `result` con los valores acumulados.​

2️⃣ **ejemplo\_while**

```rust
rustCopiarEditarpub fn ejemplo_while(env: Env) -> Vec<u32> {
    let mut count = 0;
    let mut result: Vec<u32> = Vec::new(&env);
    while count < 5 {
        result.push_back(count);
        count += 1;
    }
    result
}
```

**Descripción:**\
Genera una lista de números enteros desde 0 hasta 4 utilizando un bucle `while` que se ejecuta mientras se cumpla una condición específica.​

**Mecanismo:**

* Inicializa `count` en 0 y crea un vector vacío `result` para almacenar los resultados.​
* Emplea un bucle `while` que continúa ejecutándose mientras `count` sea menor que 5.​
* En cada iteración, añade el valor actual de `count` al vector `result` y luego incrementa `count` en 1.​
* Cuando `count` alcanza 5, la condición del `while` deja de cumplirse y el bucle termina.​
* Retorna el vector `result` con los valores acumulados.​

3️⃣ **ejemplo\_for**

```rust
rustCopiarEditarpub fn ejemplo_for(env: Env) -> Vec<String> {
    let elementos: [&str; 3] = ["a", "b", "c"];
    let mut result = Vec::new(&env);
    for elemento in elementos.iter() {
        result.push_back(String::from_str(&env, elemento));
    }
    result
}
```

**Descripción:**\
Itera sobre un array de cadenas de texto y las almacena en un vector, demostrando el uso del bucle `for` en Rust.​

**Mecanismo:**

* Define un array `elementos` con tres cadenas de texto: "a", "b" y "c".​
* Crea un vector vacío `result` para almacenar las cadenas convertidas.​
* Utiliza un bucle `for` para iterar sobre cada elemento del array `elementos`.​
* Dentro del bucle, convierte cada `&str` en un `String` utilizando `String::from_str` y el entorno `env`, y luego añade el resultado al vector `result` con `push_back`.​
* Después de procesar todos los elementos, retorna el vector `result` que contiene las cadenas "a", "b" y "c" como objetos `String`.​

📌 **Resumen General**\
El contrato **LoopContract** ejemplifica el uso de diferentes estructuras de control de flujo en Rust dentro del contexto de contratos inteligentes en Soroban. Las funciones demostradas —`loop`, `while` y `for`— ofrecen distintas maneras de iterar y manipular datos, permitiendo a los desarrolladores elegir la estructura más adecuada según las necesidades específicas de su lógica de negocio.

***

**Compilación del contrato**

Ejecutamos lo siguiente:

```
stellar contract build
```

**Despliegue del contrato**

Para Mac y Linux el salto de línea es con el carácter " **\\**" y en Windows con el carácter " **´** "

Reemplaze el simbolo \* por el respectivo carácter de salto de linea a su sistema operativo.

```bash
stellar contract deploy *
  --wasm target/wasm32-unknown-unknown/release/loops.wasm *
  --source developer *
  --network testnet *
  --alias loops
```

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Pruebas del contrato**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

**Función ejemplo\_loop:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
ejemplo_loop
```

**Función ejemplo\_while:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
ejemplo_while
```

**Función ejemplo\_for:**

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
ejemplo_for
```

