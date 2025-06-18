# Errores

## 📚 Manejo de errores

### 🎯 Parte Teórica: Fundamentos del Manejo de Errores

#### ¿Por qué es importante el manejo de errores? 🤔

En los contratos inteligentes de Soroban, el manejo de errores es **crítico** porque:

* 🛡️ **Seguridad**: Evita comportamientos inesperados que podrían ser explotados
* 💰 **Protección de fondos**: Previene pérdidas de tokens o activos
* 🎯 **Experiencia del usuario**: Proporciona mensajes claros sobre qué salió mal
* 📊 **Debugging**: Facilita la identificación y solución de problemas

#### 🔧 Tipos de Errores en Soroban

**1. Errores del Sistema (Host Errors) 🏠**

Son errores que maneja automáticamente el entorno de Soroban:

* Falta de memoria
* Límites de gas excedidos
* Operaciones matemáticas inválidas (división por cero)

**2. Errores Personalizados (Custom Errors) ✨**

Son errores que tú defines para tu lógica de negocio:

* Permisos insuficientes
* Parámetros inválidos
* Estados de contrato incorrectos

#### 🛠️ Herramientas para Manejar Errores

**Result\<T, E> - Tu mejor amigo 🤝**

```rust
// Tipo Result básico
Result<Valor_Exitoso, Tipo_de_Error>

// En Soroban usamos principalmente:
Result<ReturnType, Error>
```

**Macros útiles 🎨**

* `panic!()` - Para errores irrecuperables (¡usar con cuidado!)
* `assert!()` - Para validaciones críticas
* `log!()` - Para debugging (solo en testnet)

#### 📋 Estrategias de Manejo de Errores

**1. Validación Temprana ⚡**

Valida todos los parámetros al inicio de la función

**2. Errores Específicos 🎯**

Crea tipos de error específicos para cada situación

**3. Propagación Controlada 📤**

Usa `?` para propagar errores de forma limpia

**4. Logging Inteligente 📝**

Registra errores para facilitar el debugging

**💡 Ejemplos Prácticos:**

#### 🌱 Ejemplo : Contrato Básico SIN Errores Personalizados

Empecemos con algo  simple que usa solo los errores que Soroban ya tiene integrados:

```rust
#![no_std]
use soroban_sdk::{contract, contractimpl, contracttype, panic_with_error, Env, Symbol};
// 📦 Estructura simple para guardar datos
#[contracttype]
pub struct UserData {
    pub name: Symbol,
    pub balance: i64,
}
#[contract]
pub struct SimpleContract;

#[contractimpl]
impl SimpleContract {
    // 💾 Función para guardar datos de usuario
    pub fn set_user(env: Env, user_id: Symbol, name: Symbol, balance: i64) {
        // ✅ Validación simple usando assert! (si falla, panic automático)
        assert!(balance >= 0, "Balance no puede ser negativo");

        // 💾 Crear y guardar datos
        let user_data = UserData { name, balance };
        env.storage().persistent().set(&user_id, &user_data);
    }
    // 👀 Función para leer datos con Option (sin errores personalizados)
    pub fn get_user(env: Env, user_id: Symbol) -> Option<UserData> {
        // 🔍 Intentar obtener datos del storage
        // Si no existe, devuelve None automáticamente
        env.storage().persistent().get(&user_id)
    }
    // 💰 Función para actualizar balance con validaciones básicas
    pub fn update_balance(env: Env, user_id: Symbol, new_balance: i64) -> Option<i64> {
        // ✅ Validación simple
        if new_balance < 0 {
            // 🚨 Forma simple de manejar error: devolver None
            return None;
        }

        // 📋 Intentar obtener datos existentes
        let mut user_data: UserData = match env.storage().persistent().get(&user_id) {
            Some(data) => data,
            None => return None, // Usuario no existe
        };

        // ✅ Actualizar y guardar
        user_data.balance = new_balance;
        env.storage().persistent().set(&user_id, &user_data);

        // 🎉 Devolver el nuevo balance
        Some(new_balance)
    }
    // ➕ Función que puede fallar por overflow (usa checked_add)
    pub fn add_to_balance(env: Env, user_id: Symbol, amount: i64) -> Option<i64> {
        // 📋 Obtener datos del usuario
        let mut user_data: UserData = env.storage().persistent().get(&user_id)?; // 🎯 Usa ? con Option (si es None, devuelve None)

        // 🧮 Suma segura (evita overflow)
        let new_balance = user_data.balance.checked_add(amount)?;

        // ✅ Solo actualizar si todo salió bien
        user_data.balance = new_balance;
        env.storage().persistent().set(&user_id, &user_data);

        Some(new_balance)
    }
    // 🔍 Función helper que demuestra diferentes formas de manejar "no encontrado"
    pub fn get_balance_or_zero(env: Env, user_id: Symbol) -> i64 {
        // 🎯 Usar unwrap_or para proporcionar valor por defecto
        env.storage()
            .persistent()
            .get::<Symbol, UserData>(&user_id)
            .map(|data| data.balance) // Si existe, tomar el balance
            .unwrap_or(0) // Si no existe, usar 0
    }
}
```

### 🎯 Conceptos Clave de este Ejemplo:

#### ✅ **Herramientas Básicas de Manejo de Errores:**

1. **`Option<T>`** 📦
   * `Some(value)` cuando todo está bien
   * `None` cuando algo falla o no existe
   * Perfecto para "encontrado/no encontrado"
2. **`assert!(condición, mensaje)`** ⚡
   * Valida condiciones críticas
   * Si falla, causa panic automático
   * Ideal para validaciones que nunca deberían fallar
3. **`checked_add()`, `checked_sub()`** 🧮
   * Operaciones matemáticas seguras
   * Devuelven `Option`: `Some(result)` o `None` si hay overflow
   * Previenen errores numéricos silenciosos
4. **Operador `?` con Option** 🎯
   * Si es `Some`, continúa con el valor
   * Si es `None`, termina la función devolviendo `None`
   * Hace el código más limpio
5. **`unwrap_or(default)`** 🛡️
   * Proporciona un valor por defecto si es `None`
   * Evita crashes cuando un valor faltante es aceptable

#### 📋 **Cuándo Usar Cada Herramienta:**

* **`Option`**: Para datos que pueden o no existir
* **`assert!`**: Para condiciones que NUNCA deberían ser falsas
* **`checked_*`**: Para operaciones matemáticas que pueden overflow
* **`?`**: Para propagar "falta de datos" de forma limpia
* **`unwrap_or`**: Cuando un valor faltante tiene un reemplazo lógico

#### 🏗️ Ejemplo : Contrato Básico con Errores Personalizados

Este ejemplo muestra un contrato simple para gestionar un contador con validaciones:

**En proceso...**
