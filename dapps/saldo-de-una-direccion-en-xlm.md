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

Dentro de la carpeta src cambiamos el siguiente código en el archivo layout.tsx

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

WalletButton:  Es el resposable de accesar a la billetera y saber su dirección









