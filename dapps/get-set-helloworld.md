# get set helloworld

Esta es la interfaz del contrato  [MessageContract](../soroban/contratos-ejemplo/get-set-helloworld.md). La cual es una operación de poner un valor tipo string a un contrato y capturar tambien este valor.\
el contrato desplegado es el :&#x20;

[CBLHRYKD6UPJA75PJ5CCTCY6LO4H3ZY7HFW2SS2JP4US5VRYDEO3I67H](https://stellar.expert/explorer/testnet/contract/CBLHRYKD6UPJA75PJ5CCTCY6LO4H3ZY7HFW2SS2JP4US5VRYDEO3I67H)

\
para realizar los pasos iniciales , es exactamente los mismos pasos  iniciales del la dapp [Saldo de una dirección en XLM](saldo-de-una-direccion-en-xlm.md).

Una vez instalado Next o el entorno favorito, y dejarlo listo, procedemos a abrir la terminal e instalar los siguientes paquetes:

```
 npm install react-hot-toast
 npm install @stellar/stellar-sdk
 npm install stellar-react
```

[**react-hot-toast:**](https://react-hot-toast.com/)  Sirve para dar mensajes  al usuario  acerca de alguna operación

[**@stellar/stellar-sdk:**](https://stellar.github.io/js-stellar-sdk/)  Libreria  en javascript para interactuar con la red de stellar, en esta ocasión la usaremos para poder convertir nuestros valores de los mensajes al entandar de mensajes de stellar [XDR](https://developers.stellar.org/docs/data/apis/horizon/api-reference/structure/xdr)

[**stellar-react:**](https://github.com/paltalabs/stellar-react) Los amigos de la empresa [Palta Labs](https://paltalabs.io/) 🥑, una vez más al rescate, extendiendo la libreria de [Stellar-wallets-kits](https://stellarwalletskit.dev/)  permitiendo interaractuar con los contratos  y la operación de conexión a la billetera  de una forma más sencilla ( developer friendly 🤓)

### Archivos a crear o modificar

**StellarWalletProvider.tsx:** En la ruta src/app

Sirve para la configuración inicial de la billetera, red por defecto, el contrato que vamos a usar, esta configuración la vamos a necesitar a través de toda la aplicación

```tsx
// Este archivo define un Provider personalizado para conectar una aplicación Next.js con la red de Stellar usando Soroban React.

'use client'; // Indica que este componente se ejecuta del lado del cliente en Next.js
import { ReactNode } from "react";
import { NetworkDetails, SorobanReactProvider, WalletNetwork } from "stellar-react";
import deployments from './deployments.json'; // Importa la configuración de despliegue de contratos

// Componente Provider que envuelve la aplicación y provee acceso a la red de Stellar
const StellarWalletProvider = ({ children }: { children: ReactNode }) => {

    // Definimos los detalles de la red de prueba (testnet) de Stellar
    // Puedes cambiar estos valores para conectar a otra red si lo necesitas
    const testnetNetworkDetails: NetworkDetails = {
        network: WalletNetwork.TESTNET, // Selecciona la red de prueba de Stellar
        sorobanRpcUrl: 'https://soroban-testnet.stellar.org/', // URL del nodo Soroban para testnet
        horizonRpcUrl: 'https://horizon-testnet.stellar.org' // URL de Horizon para testnet
    }
    return (
        // SorobanReactProvider provee el contexto de Stellar a toda la app
        <SorobanReactProvider
            appName={"Hello World Stellar App"} // Nombre de la app que se muestra en la wallet
            allowedNetworkDetails={[testnetNetworkDetails]} // Lista de redes permitidas (aquí solo testnet)
            activeNetwork={WalletNetwork.TESTNET} // Red activa por defecto
            deployments={deployments} // Información de despliegue de contratos inteligentes
        >
            {/* children representa los componentes hijos que tendrán acceso al contexto de Stellar */}
            {children}
        </SorobanReactProvider>
    )
}
// Exportamos el Provider para usarlo en el layout principal de la app
export default StellarWalletProvider;
```

**layout.tsx:** En la ruta src/app

Es nuestro "template" de la aplicación, allí indicamos que toda la aplicación va a tener acceso a los datos de la billetera, billetera conectada, red, servidor al que está conectado, etc...

```tsx
// Este archivo define el layout principal de la aplicación Next.js.
// Aquí se configuran los estilos globales, fuentes, providers y la estructura base de la app.


import type { Metadata } from "next"; // Importa el tipo Metadata para definir metadatos de la página
import { Geist, Geist_Mono } from "next/font/google"; // Importa fuentes de Google para mejorar la apariencia
import "./globals.css"; // Importa los estilos globales de la aplicación
import StellarWalletProvider from "./StellarWalletProvider"; // Importa el provider personalizado para conectar con la red de Stellar
import { Toaster } from "react-hot-toast"; // Importa el componente para mostrar notificaciones tipo toast

// Configuración de la fuente sans-serif personalizada usando Geist
const geistSans = Geist({
  variable: "--font-geist-sans",
  subsets: ["latin"],
});

// Configuración de la fuente monoespaciada personalizada usando Geist Mono
const geistMono = Geist_Mono({
  variable: "--font-geist-mono",
  subsets: ["latin"],
});

// Definición de los metadatos de la aplicación (título y descripción)
export const metadata: Metadata = {
  title: "Get set messaje",
  description: "get y set de una variable de contratos",
};

// Componente principal del layout
// children representa el contenido de cada página que se renderiza dentro del layout
export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body
        className={`${geistSans.variable} ${geistMono.variable} antialiased`}
      >
        {/*
          StellarWalletProvider envuelve toda la aplicación y provee el contexto de conexión a la red de Stellar.
          Esto permite que cualquier componente hijo pueda interactuar con la blockchain de Stellar fácilmente.
        */}
        <StellarWalletProvider>{children}</StellarWalletProvider>
        {/*
          Toaster se utiliza para mostrar notificaciones emergentes (toast) en la interfaz.
          El div con id="toast-root" es el contenedor donde se renderizan estos mensajes.
        */}
        <div id="toast-root"><Toaster/></div>
      </body>
    </html>
  );
}
```

**deployments.json:** En la ruta src/app

array json donde podemos almacenar los diferentes contratos con que podemos interactuar.

```json
[
{
    "contractId": "hello_world",
    "networkPassphrase": "Test SDF Network ; September 2015",
    "contractAddress": "CBLHRYKD6UPJA75PJ5CCTCY6LO4H3ZY7HFW2SS2JP4US5VRYDEO3I67H"
  }
]
```

**page.tsx:** En la ruta src/app

Página principal de la aplicación, a resaltar allí la interactividad es nula, ya que está se da en el componente Header y TablaValores .

```tsx
// Este archivo define la página principal de la aplicación, integrando los componentes de encabezado y tabla de valores.
import Header from "./components/Header";
import TablaValores from "./components/TablaValores";
import React from "react";

// Componente principal de la página Home
export default function Home() {
  return (
    // Estructura principal de la página con estilos y disposición
    <main className="min-h-screen bg-black flex flex-col items-center justify-start">
      {/* Componente de encabezado que gestiona la conexión de la billetera */}
      <Header />
      <section className="w-full max-w-2xl mt-10 px-4">
        <div className="bg-gray-900 bg-opacity-80 rounded-xl shadow-[0_4px_24px_0_rgba(255,255,255,0.15)] border border-gray-700 p-8">
          <h2 className="text-3xl font-bold text-white mb-2 text-center">Get Set Message Stellar Español</h2>
          <p className="text-gray-300 text-center mb-8">
            Esta aplicación permite recuperar y establecer un valor tipo string en un contrato inteligente sobre la red Stellar. 
          </p>
          <p className="text-gray-400 text-center mb-8 font-mono">
            Contrato: CBLHRYKD6UPJA75PJ5CCTCY6LO4H3ZY7HFW2SS2JP4US5VRYDEO3I67H
          </p>
          {/* Componente que muestra y permite actualizar el valor del contrato */}
          <TablaValores />
        </div>
      </section>
    </main>
  );
}
```

📝 <mark style="color:green;">**A continuación  se  crea la carpeta components en la ruta src/app**</mark>

**Header.tsx:** En la ruta src/app/components

Es el encargado de la conexión y desconexión de la billetera que tengamos instalada al navegador de la red de stellar

```tsx
// Este componente Header muestra el título de la aplicación y permite conectar o desconectar la billetera Stellar.
'use client';
import React, { useState } from "react";
import { useSorobanReact } from "stellar-react";

// Componente principal del encabezado
const Header: React.FC = () => {
  // Extraemos la dirección de la billetera, y las funciones para conectar y desconectar usando el hook de Soroban React
  const { address, connect, disconnect } = useSorobanReact();
  return (
    // Encabezado visual con estilos y título
    <header className="w-full flex justify-between items-center px-6 py-4 bg-black bg-opacity-80 border-b border-gray-800">
      <h1 className="text-2xl font-bold text-white">Stellar Wallet</h1>
      {/* Si no hay dirección, mostramos el botón para conectar la billetera */}
      {!address ? (
        <button
          className="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded transition"
          onClick={connect}
        >
          Conectar billetera
        </button>
      // Si hay dirección, mostramos la dirección y el botón para desconectar */}
      ) : (
        <div className="flex items-center gap-4">
          {/* Mostramos la dirección de la billetera conectada */}
          <span className="text-gray-300 text-sm">{address}</span>
          <button
            className="bg-blue-400 hover:bg-blue-500 text-white px-4 py-2 rounded transition shadow-lg shadow-white/30"
            onClick={disconnect}
          >
            Desconectar
          </button>
        </div>
      )}
    </header>
  );
};

// Exportamos el componente para su uso en otras partes de la app
export default Header;
```

**TablaValores.tsx:** En la ruta src/app/components

Es el componente estrella de la daap ⭐, sobre todo las funciones:

* **readMessage**: Invoca el metodo get\_message del contrato

Algo relevante es que pasamos el mensaje de su [almacenamiento del contrato a un tipo nativo](https://stellar.github.io/js-stellar-sdk/global.html#scValToNative)

```typescript
 // Obtenemos la dirección del contrato y llamamos al método get_message
      const addr = contract?.deploymentInfo?.contractAddress;
      const result = await contract?.invoke({ method: "get_message", args: [] });
      setMessage(scValToNative(result as xdr.ScVal) as string)
```

* writeMessageInvoca el metodo set\_message del contrato. &#x20;

Pasamos el mensaje de nativo a  [valor XDR](https://stellar.github.io/js-stellar-sdk/global.html#nativeToScVal)

```typescript
// Llamamos al método set_message del contrato, firmando la transacción
      const result = await contract?.invoke({
        method: "set_message",
        args: [nativeToScVal(newMessage, { type: "string" })],
        signAndSend: true,
        reconnectAfterTx: false
      });
```

```tsx
// Este componente permite leer y escribir un mensaje en un contrato inteligente de Stellar.
'use client';
import { nativeToScVal, scValToNative, xdr } from "@stellar/stellar-sdk";
import React, { useEffect, useState } from "react";
import toast from "react-hot-toast";
import { useRegisteredContract, useSorobanReact } from "stellar-react";

// Componente principal de la tabla de valores
const TablaValores: React.FC = () => {
  // Obtenemos información de la red, el servidor y la dirección de la billetera
  const { activeNetwork, sorobanServer, address } = useSorobanReact();
  // Obtenemos el contrato registrado con el nombre "hello_world"
  const contract = useRegisteredContract("hello_world");
  // Estado para el mensaje actual y el nuevo mensaje a enviar
  const [message, setMessage] = useState("");
  const [newMessage, setNewMessage] = useState("");

  // Función para leer el mensaje almacenado en el contrato inteligente
  const readMessage = async () => {
    if (!contract) {
      return;
    }
    try {
      // Obtenemos la dirección del contrato y llamamos al método get_message
      const addr = contract?.deploymentInfo?.contractAddress;
      const result = await contract?.invoke({ method: "get_message", args: [] });
      setMessage(scValToNative(result as xdr.ScVal) as string)
    }
    catch (error) {
      console.log(error);
    }
  }
  // Función para escribir un nuevo mensaje en el contrato inteligente
  const writeMessage = async () => {
    // Validamos que la billetera esté conectada
    if (address === undefined) {
      toast.error("Wallet no conectada");
      return;
    }
    // Validamos que el campo no esté vacío
    if (newMessage === "") {
      toast.error("Introduce un mensaje");
      return;
    }
    try {
      // Llamamos al método set_message del contrato, firmando la transacción
      const result = await contract?.invoke({
        method: "set_message",
        args: [nativeToScVal(newMessage, { type: "string" })],
        signAndSend: true,
        reconnectAfterTx: false
      });
      if (result) {
        toast.success("mensaje actualizado");
        readMessage(); // Actualizamos el mensaje mostrado
      } else {
        toast.error("Actuliazacion fallida");
      }
    } catch (error) {
      if (error instanceof Error) {
        console.log(`Error: ${error.message}`);
      }
      else {
        console.log('ocurrio un error desconocido');
      }
    }
  }
  
  // Hook useEffect para leer el mensaje cada vez que cambia el servidor o el contrato
  useEffect(() => {
    readMessage();
  }, [sorobanServer, contract]);
  return (
    // Tabla visual para mostrar y actualizar el valor del contrato
    <div className="w-full max-w-md mx-auto mt-8 bg-gray-900 bg-opacity-80 rounded-lg shadow-lg p-6">
      <table className="w-full text-white border-separate border-spacing-y-4">
        <tbody>
          <tr>
            <td className="font-semibold">Valor actual</td>
            <td className="bg-gray-800 rounded px-4 py-2 text-center">{message}</td>
          </tr>
          <tr>
            <td className="font-semibold">Valor nuevo</td>
            <td>
              <input
                type="text"
                className="w-full px-3 py-2 rounded bg-gray-700 text-white focus:outline-none focus:ring-2 focus:ring-blue-600"
                value={newMessage}
                onChange={e => setNewMessage(e.target.value)}
                placeholder="Introduce un nuevo valor"
              />
            </td>
          </tr>
          <tr>
            <td></td>
            <td>
              <button
                className="w-full bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded transition mt-2"
                onClick={writeMessage}
              >
                Enviar
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  );
};

// Exportamos el componente para su uso en otras partes de la app
export default TablaValores;
```

Para ejecutar el programa ejecutamos:

```bash
npm run dev
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Programa en ejecución</p></figcaption></figure>

Todo el proyecto esta en un repositorio[ github](https://github.com/stellarenespanol/get_set_hello_world.git)
