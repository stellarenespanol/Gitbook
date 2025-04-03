# Sentencias condicionales

### 📚 **Teoría sobre `if`, `else` y `match` en Rust**

Rust ofrece dos estructuras de control clave para la toma de decisiones: **if-else** y **match**.

#### ✅ **`if-else`**

Se usa para **tomar decisiones en función de una condición booleana**.

📌 **Sintaxis básica:**

```rust
rustCopiarEditarif condicion {
    // Código si la condición es verdadera
} else {
    // Código si la condición es falsa
}
```

📌 **Ejemplo en Rust:**

```rust
rustCopiarEditarlet num = 10;
if num > 0 {
    println!("El número es positivo");
} else {
    println!("El número es negativo");
}
```

📌 **Cuándo usar `if-else`**

* Cuando se necesita verificar una condición **booleana única**.
* Cuando solo hay **dos opciones** (`true` o `false`).

***

#### ✅ **`match`**

Se usa para **comparar un valor con múltiples posibles casos**, similar a `switch` en otros lenguajes.

📌 **Sintaxis básica:**

```rust
rustCopiarEditarmatch variable {
    valor1 => { /* Código para valor1 */ },
    valor2 => { /* Código para valor2 */ },
    _      => { /* Código por defecto */ },
}
```

📌 **Ejemplo en Rust:**

```rust
rustCopiarEditarlet rol = "Admin";
match rol {
    "Admin" => println!("Acceso total"),
    "User"  => println!("Acceso limitado"),
    "Guest" => println!("Solo lectura"),
    _       => println!("Rol no reconocido"),
}
```

📌 **Cuándo usar `match`**

* Cuando hay **varias condiciones posibles**.
* Cuando se trabaja con **enum, strings o patrones complejos**.



### Código en Soroban



Nos vamos a una ruta donde queramos crear nuestro proyecto y ponemos el siguiente comando:

```
stellar contract init  conditionalStatements --name ConditionalStatements
```

Borramos el contrato generado en  lib.rs y ponemos el siguiente código

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, symbol_short, Env, String, Symbol};
#[contract]
pub struct ConditionalSt;

#[contractimpl]
impl ConditionalSt {
    // Función que usa if-else para determinar si un número es positivo o negativo.
    pub fn check_number(env: Env, num: i32) -> String {
        if num >= 0 {
            String::from_str(&env, "Numero positivo")
        } else {
            String::from_str(&env, "Numero negativo")
        }
    }

    // Función que usa match para asignar permisos según el rol del usuario.
    pub fn check_role(env: Env, role: Symbol) -> String {
        let admin: Symbol = symbol_short!("Admin");
        let user: Symbol = symbol_short!("User");
        let guest: Symbol = symbol_short!("Guest");

        match role {
            admin => String::from_str(&env, "Acceso total"),
            user => String::from_str(&env, "Acceso limitado"),
            guest => String::from_str(&env, "Solo lectura"),
            _ => String::from_str(&env, "Rol no reconocido"),
        }
    }
}
```

### 📌 **Explicación general del código**

* Usa `#![no_std]`, lo que significa que **no usa la biblioteca estándar de Rust**.
* Importa módulos de `soroban_sdk`, necesarios para escribir **contratos inteligentes en Soroban**.
* Define una **estructura de contrato** llamada `ConditionalSt`.
* Implementa dos funciones clave:
  1. `check_number()`: Usa `if-else` para verificar si un número es **positivo o negativo**.
  2. `check_role()`: Usa `match` para **asignar permisos** según el rol de un usuario.

***

🛠 **Explicación de las Funciones**

1️⃣ **check\_number**

```rust
pub fn check_number(env: Env, num: i32) -> String {
        if num >= 0 {
            String::from_str(&env, "Numero positivo")
        } else {
            String::from_str(&env, "Numero negativo")
        }
    }
```

**Descripción:**\
Determina si un número es positivo o negativo.

**Mecanismo:**

* Recibe un número entero (i32) como entrada.
* Emplea una estructura condicional **if-else** para evaluar el valor de `num`.
* Si `num` es mayor o igual a 0, retorna un `String` con el mensaje **"Numero positivo"**.
* Si `num` es negativo, retorna un `String` con el mensaje **"Numero negativo"**.

2️⃣ **check\_role**

```rust
pub fn check_role(env: Env, role: Symbol) -> String {
        let admin: Symbol = symbol_short!("Admin");
        let user: Symbol = symbol_short!("User");
        let guest: Symbol = symbol_short!("Guest");

        match role {
            admin => String::from_str(&env, "Acceso total"),
            user => String::from_str(&env, "Acceso limitado"),
            guest => String::from_str(&env, "Solo lectura"),
            _ => String::from_str(&env, "Rol no reconocido"),
        }
    }
}
```

**Descripción:**\
Asigna permisos según el rol del usuario.

**Mecanismo:**

* Define tres roles fijos utilizando el macro `symbol_short!`:
  * **Admin**
  * **User**
  * **Guest**
* Utiliza una estructura de control **match** para comparar el `role` recibido con los roles definidos.
* Retorna un `String` con el permiso correspondiente:
  * **"Acceso total"** si el rol es **Admin**.
  * **"Acceso limitado"** si el rol es **User**.
  * **"Solo lectura"** si el rol es **Guest**.
* Si el rol no coincide con ninguno de los predefinidos, retorna **"Rol no reconocido"**.

📌 **Resumen General**\
Este contrato inteligente demuestra el uso de condicionales y estructuras de control en Soroban. A través de **check\_number** se evalúa la positividad o negatividad de un número, mientras que **check\_role** asigna permisos basándose en roles específicos.

***

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
  --wasm target/wasm32-unknown-unknown/release/ConditionalStatements.wasm *
  --source developer *
  --network testnet *
  --alias ConditionalStatements
```

<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

**Pruebas del contrato**

Para **Linux y Mac** el salto de línea de la instrucción es con el carácter " \ " para **Windows** con el carácter " \` "

**Función** get\_length

```bash
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
check_number *
--num 10
```

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

**Función check\_role**

```
stellar contract invoke *
--id <CONTRACT_ID> *
--source <Identity> *
--network testnet *
-- *
check_role *
--role "Admin"
```

<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>
