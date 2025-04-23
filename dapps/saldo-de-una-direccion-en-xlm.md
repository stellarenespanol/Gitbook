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



