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

