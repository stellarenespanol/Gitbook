# Saldo de una dirección en XLM

A continuación vamos a realizar nuestro primer ejercicio en react  😃

Consiste en realizar una conexión  usando la libreria de [Stellar-Wallets-Kit](https://stellarwalletskit.dev/) que nos permite integrarnos a varias billeteras y el uso del servicio api rest de[ stellar horizon ](https://developers.stellar.org/es/docs/data/apis/horizon) el cual ofrece varios servicios,  para este caso en particular la lectura de saldo de una dirección en particular.

Como es un ejercicio de una sola página, vamos a usar Vite, que una herramienta ágil y sencilla para hacer [SPA](https://es.wikipedia.org/wiki/Single-page_application)

### <sub>**Creación del proyecto**</sub>

<sub>Nos ubicamos en la ubicación que deseemos abrimos consola y ejecutamos:</sub>

```bash
yarn create vite
```

A continuación el prompt hace varias preguntas:

Project name:\
│ Wallet-Balance\
│\
◇ Package name:\
│ wallet-balance\
│\
◇ Select a framework:\
│ React\
│\
◇ Select a variant:\
│ [TypeScript + SWC](https://blog.nubecolectiva.com/que-significa-typescript-swc-en-vite-js/)

A continuación entramos al directorio de Wallet balance y abrimos ese folder con visual studio code o tu editor favorito.

Para darle un poco de estilo vamos a utilizar [Tailwind CSS](https://tailwindcss.com/)

Lo que hacemos es seguir las instrucciones de la guia oficial , instalar [tailwind como plugin de vite](https://tailwindcss.com/docs/installation/using-vite). **Cómo estamos usando yarn, cambianos la instrucción de npm por yarn,  ya que no debemos mezclar manejadores de paquetes.**

**1 Instalar tailwind**

```bash
 yarn add tailwindcss @tailwindcss/vite
```

**2 configurar el plugin en el archivo** vite.config.ts

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react-swc'
import tailwindcss from '@tailwindcss/vite'

// https://vite.dev/config/
export default defineConfig({
  plugins: [react(),tailwindcss()],
})
```

**3 Modificar el archivo index.css:**

&#x20;Se ubica en la carpeta src.&#x20;

Agregamos en la primera linea

```css-extras
@import "tailwindcss";
```

**4 Ensayando que todo esta bien instalado:**

Dentro  de la  carpeta src,  modificamos el archivo App.tsx

```jsx
function App() {

  return (
    <>
      <h1 className="text-3xl font-bold underline">
        Hello world!
      </h1>
    </>
  )
}

export default App

```









