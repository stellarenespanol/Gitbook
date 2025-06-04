# Saldo de una dirección en XLM

A continuación vamos a realizar nuestro primer ejercicio en react  😃

Consiste en realizar una conexión  usando la libreria de [Stellar-Wallets-Kit](https://stellarwalletskit.dev/) que nos permite integrarnos a varias billeteras y el uso del servicio api rest de[ stellar horizon ](https://developers.stellar.org/es/docs/data/apis/horizon) el cual ofrece varios servicios,  para este caso en particular la lectura de saldo de una dirección en particular.

Por recomendaciones de la documentación de react, como indican usar un framework, usaremos el de [Next.js](https://nextjs.org/) , que es uno de los más populares.

### <sub>**Creación del proyecto**</sub>

<sub>Nos ubicamos en la ubicación que deseemos abrimos consola y ejecutamos:</sub>

```bash
npx create-next-app@latest
```

A continuación el prompt hace varias preguntas:

```bash
√ What is your project named? ... wallet-balance
√ Would you like to use TypeScript? ... No / Yes
√ Would you like to use ESLint? ... No / Yes
√ Would you like to use Tailwind CSS? ... No / Yes
√ Would you like your code inside a src/ directory? ... No / Yes
√ Would you like to use App Router? (recommended) ... No / Yes
√ Would you like to use Turbopack for next dev? ... No / Yes
√ Would you like to customize the import alias (@/* by default)? ... No / Yes
```

Entramos en la carpeta y en consola ponemos el siguiente comando:

```bash
npm install @creit.tech/stellar-wallets-kit
```

Con esto ya tenemos todas instalaciones necesarias para este proyecto 😃

Abrimos  el directorio de wallet-balance  con visual studio code o tu editor favorito

Dentro de la carpeta src/app cambiamos el siguiente código en el archivo layout.tsx

```tsx
export const metadata: Metadata = {
  title: "Wallet balance",
  description: "Mi primera daap gracias a Stellar español",
};
```

También cambiamos el contenido del archivo page.tsx

```tsx
// Archivo principal de la aplicación: muestra la interfaz principal y conecta los componentes clave
// "use client" indica que este archivo se ejecuta del lado del cliente en Next.js
'use client'
import React, { useState } from "react";
import WalletButton from "./components/WalletButton";
import Balance from "./components/Balance";

/**
 * Componente principal Home
 * Este componente representa la página principal de la aplicación.
 * Permite al usuario conectar su wallet de Stellar y ver el saldo de su cuenta.
 * Utiliza estados locales para manejar la conexión y la dirección de la cuenta.
 */
export default function Home() {
  // Estado que indica si la wallet está conectada (true/false)
  const [isConnected, setIsConnected] = useState(false);
  // Estado que almacena la dirección pública de la cuenta Stellar conectada
  const [address, setAddress] = useState<string | null>(null);

  // Renderiza la interfaz principal de la aplicación
  return (
    <>
      <main className="flex justify-center items-start min-h-screen bg-black py-12">
        <div className="bg-black bg-opacity-90 rounded-xl shadow-[0_4px_32px_0_rgba(255,255,255,0.25)] border border-white/20 px-8 py-10 max-w-xl w-full text-center">
          {/* Título principal */}
          <h1 className="text-3xl md:text-4xl font-bold text-white mb-4">Bienvenido a Stellar en español</h1>
          {/* Descripción de la aplicación */}
          <p className="text-base md:text-lg text-gray-200">
            Esta aplicación te permite consultar de forma rápida y sencilla el saldo de tu cuenta en la red Stellar. Conecta tu wallet y visualiza al instante cuántos XLM tienes disponibles en tu cuenta.
          </p>
          {/* Botón para conectar/desconectar la wallet */}
          <WalletButton isConnected={isConnected} address={address} setIsConnected={setIsConnected} setAddress={setAddress} />
          {/* Componente que muestra el saldo si la wallet está conectada */}
          <Balance isConnected={isConnected} address={address} />
          
        </div>
      </main>
     
    </>
  );
}

```

Como podemos ver la "magia" del programa está los  componentes:



1. **WalletButton**:  Es el resposable de accesar a la billetera y saber su dirección
2. **Balance:** una vez tenemos la dirección, mediante  el api rest de horizon, obtenemos el saldo.

Dentro de la carpeta src/app creamos la carpeta utils y components

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption><p>Estructura de directorios sugerido</p></figcaption></figure>

Dentro de components\
Crear el archivo WalletButton.tsx

```tsx
// Componente WalletButton: permite conectar y desconectar una wallet de Stellar
// "use client" indica que este componente se ejecuta del lado del cliente en Next.js
'use client'
import React from "react";
import {
    StellarWalletsKit,
    WalletNetwork,
    allowAllModules,
    FREIGHTER_ID,
    ISupportedWallet
} from '@creit.tech/stellar-wallets-kit';

/**
 * Props que recibe el componente WalletButton
 * @property {boolean} isConnected - Indica si la wallet está conectada
 * @property {string | null} address - Dirección de la cuenta Stellar
 * @property {function} setIsConnected - Función para actualizar el estado de conexión
 * @property {function} setAddress - Función para actualizar la dirección
 */
interface WalletButtonProps {
    isConnected: boolean; // Indica si la wallet está conectada
    address: string | null; // Dirección de la cuenta Stellar
    setIsConnected: React.Dispatch<React.SetStateAction<boolean>>; // Función para actualizar el estado de conexión
    setAddress: React.Dispatch<React.SetStateAction<string | null>>; // Función para actualizar la dirección
}

/**
 * Componente funcional que maneja la conexión y desconexión de la wallet de Stellar.
 * Permite al usuario conectar su wallet, ver la dirección y desconectarla.
 * Utiliza el kit de wallets de Stellar para facilitar la integración.
 */
const WalletButton: React.FC<WalletButtonProps> = ({ isConnected, address, setIsConnected, setAddress }) => {
    // Inicializa el kit de wallets de Stellar para la red de prueba (TESTNET)
    const kit: StellarWalletsKit = new StellarWalletsKit({
        network: WalletNetwork.TESTNET,
        selectedWalletId: FREIGHTER_ID,
        modules: allowAllModules(),
    });

    /**
     * Función para conectar la wallet
     * Abre un modal para seleccionar la wallet y obtiene la dirección de la cuenta
     */
    const handleConnect = async () => {
        // Abre el modal para seleccionar la wallet
        await kit.openModal({
            onWalletSelected: async (option: ISupportedWallet) => {
                kit.setWallet(option.id); // Selecciona la wallet elegida
                const { address } = await kit.getAddress(); // Obtiene la dirección de la cuenta
                if (address) {
                    setAddress(address); // Actualiza la dirección en el estado
                    setIsConnected(true); // Marca como conectada
                }
            }
        });
    };

    /**
     * Función para desconectar la wallet
     * Limpia el estado de conexión y la dirección
     */
    const handleDisconnect = () => {
        setIsConnected(false); // Marca como desconectada
        setAddress(null); // Limpia la dirección
    };

    // Renderiza el botón correspondiente según el estado de conexión
    return (
        <div className="mt-6 flex flex-col items-center">
            {isConnected ? (
                <>
                    {/* Muestra la dirección conectada y botón para desconectar */}
                    <p className="text-white mb-2">Conectado como:</p>
                    <p className="text-white font-mono mb-4">{address}</p>
                    <button
                        className="bg-blue-400 hover:bg-blue-500 text-white px-4 py-2 rounded"
                        onClick={handleDisconnect}
                    >
                        Desconectar Wallet
                    </button>
                </>
            ) : (
                // Botón para conectar la wallet
                <button
                    className="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded"
                    onClick={handleConnect}
                >
                    Conectar Wallet
                </button>
            )}
        </div>
    );
};

// Exporta el componente para que pueda ser usado en otras partes de la app
export default WalletButton;
```

Crear el archivo Balance.tsx

```tsx
/ Componente Balance: muestra el saldo de XLM de la cuenta conectada
// "use client" indica que este componente se ejecuta del lado del cliente en Next.js
'use client'
// Importamos React y useEffect para manejar el ciclo de vida del componente
import React, { useEffect } from "react";
// Importamos la función GetBalance que consulta el saldo en la blockchain
import GetBalance from "../utils/balance";

// Definición de las propiedades (props) que recibe el componente Balance
// Estas props permiten saber si la wallet está conectada y cuál es la dirección de la cuenta
interface BalanceProps {
  isConnected: boolean; // Indica si la wallet está conectada
  address: string | null; // Dirección de la cuenta Stellar
}

// Componente funcional que recibe las props definidas arriba
const Balance: React.FC<BalanceProps> =  ({ isConnected, address }) => {
  // Estado local para guardar el saldo obtenido de la cuenta
  // useState inicializa el saldo en 0
  const [balance, setBalance] = React.useState(0);
 
  // useEffect se ejecuta cada vez que cambian isConnected o address
  // Sirve para actualizar el saldo cuando la wallet se conecta o cambia de cuenta
  useEffect(() => {
      // Si la wallet está conectada y hay dirección, consulta el saldo
    if (isConnected && address) {
      // Llama a la función GetBalance y actualiza el estado con el resultado
      GetBalance(address).then((result) => {
        setBalance(result); // Actualiza el saldo en el estado
      });
     
    }
  }, [isConnected, address]); // Dependencias: se ejecuta cuando cambian estos valores
  
  // Si no está conectada la wallet o no hay dirección, no muestra nada
  if (!isConnected || !address) {
    return null; // No renderiza nada
  }

  // Renderiza el saldo en pantalla si la wallet está conectada
  return (
    <div className="mt-4 text-white text-lg font-semibold">
      {/* Muestra el saldo en XLM */}
      Su saldo en XLM es: {balance}
    </div>
  );
};

// Exporta el componente para que pueda ser usado en otras partes de la app
export default Balance;
```

Dentro de la carpeta utils

Creamos el archivo balance.ts

```typescript
// Función asíncrona que obtiene el saldo de XLM de una cuenta Stellar
// Esta función es fundamental para consultar el saldo de una cuenta en la blockchain de Stellar.
// Recibe como parámetro la dirección de la cuenta y devuelve el saldo en XLM.
// Si la dirección es inválida o no se encuentra saldo, retorna 0.
//
// Parámetros:
//   address (string): Dirección pública de la cuenta Stellar a consultar.
//
// Retorna:
//   Promise<number>: Saldo de la cuenta en XLM (puede ser 0 si no hay fondos o la cuenta no existe).
async function GetBalance(address: string) {
    // Verifica que la dirección no sea nula o vacía
    if (address) {
        // Realiza una petición HTTP a la API de Stellar para obtener los datos de la cuenta
        const response = await fetch(`https://horizon-testnet.stellar.org/accounts/${address}`);
       
        // Si la respuesta es exitosa (status 200)
        if (response.ok) {
            // Convierte la respuesta a formato JSON
            const data = await response.json();
            // Busca el objeto que representa el saldo nativo (XLM) dentro del array de balances
            const xlmBalance = data.balances.find((b: any) => b.asset_type === "native");
            // Retorna el saldo si es mayor a 0, si no, retorna 0
            return xlmBalance.balance > 0 ? xlmBalance.balance : 0;
        }
    }
    // Si la dirección es inválida o la petición falla, retorna 0
    return 0;
}
// Exporta la función para que pueda ser utilizada en otros archivos
export default GetBalance;
```

Para ejecutar el programa ejecutamos:

```bash
npm run dev
```

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>Ejecución del programa 😁</p></figcaption></figure>

Todo el proyecto esta en un repositorio[ github](https://github.com/stellarenespanol/Wallet-Balance.git)
