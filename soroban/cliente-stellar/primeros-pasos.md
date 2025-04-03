# 1️ Primeros pasos

El cliente de Stellar es super importante en la escritura de contratos en Stellar, ya que se encarga de la creación de la estructura básica y archivos, su compilación, despliegue y pruebas respectivas.

En esta primera pare veremos lo siguiente:

* Creación de una cuenta (identidad)
* Obtención de la llave pública (address)
* Consultar el saldo
* Envio de lumens a otra cuenta

**Creación de una cuenta (identidad)**

La identidad es un alias , que nos sirve para manejar la cuenta generada de una forma mucho más sencilla.

Para este caso por ejemplo vamos a generar una identidad llamada _developer_ , ya dejemos descansar a Bob y Alice 😅

```bash
stellar keys generate --global developer --network testnet --fund
```

Para este caso generamos una identidad global llamada developer en la red de testnet.

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption><p>Ejecucuón de prueba</p></figcaption></figure>

Al abrir el archivo generado vemos que es un contenedor de una frase semilla.

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

O  con linea de comando

```bash
cat /home/<USUARIO>/.config/stellar/identity/developer.toml
```

Más información del comando

{% embed url="https://developers.stellar.org/docs/tools/developer-tools/cli/stellar-cli#stellar-keys-generate" %}

**Obtención de la llave pública (address)**

Para saber cual es la llave pública de una identidad corremos el siguiente comando:

```bash
stellar keys address developer
```

Obtenemos:

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>

**Consultar el saldo**

Hay 2 formas por web o por comando

**El modo sencillo, explorador web**\
ingresamos la dirección en esta dirección [https://stellar.expert/explorer/testnet](https://stellar.expert/explorer/testnet)

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption><p>Acá vemos que la cuenta viene con 10.000 lumens, lo suficiente para seguir con los ejercicios</p></figcaption></figure>

**Para los amantes de la consola.** 😉

1. Averiguamos en testnet cual es el id de XLM `stellar contract id asset --asset native --network testnet`\
   obtenemos esta respuesta: _CDLZFC3SYJYDZT7K67VZ75HPJVIEUVNIXF47ZG2FB2RMQQVU2HHGCYSC_
2. Obtenemos la llave pública de la identidad developer\
   `stellar keys address developer`\
   obtenemos:\
   &#xNAN;_&#x47;D45T2VRMYBSGRHLMVTS4QQZVXAM7WD6IYWKYRS7DFURRR2EKWCNGOAN_
3. Ejecutamos los siguiente:\
   Sintaxis:\
   &#xNAN;_`stellar contract invoke --id "dirección XLM" --network testnet --source-account developer -- balance --id "llave pública developer"`_

Escribimos lo siguiente:

```bash
stellar contract invoke --id CDLZFC3SYJYDZT7K67VZ75HPJVIEUVNIXF47ZG2FB2RMQQVU2HHGCYSC 
--network testnet --source-account developer 
-- balance --id GD45T2VRMYBSGRHLMVTS4QQZVXAM7WD6IYWKYRS7DFURRR2EKWCNGOAN
```

La respuesta es:

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

**Pasar fondos de una billetera a otra**

Vamos a pasar 100 XLM de la cuenta developer a developer1.

Sintaxis:

_`stellar contract invoke --id <asset_contract_ID> --networdk testnet --source-account developer -- transfer --to <developer1_ID> --from developer --amount 100`_

Como es de observar no hemos creado la identidad developer1 por lo tanto ejecutamos:\
`stellar keys generate --global developer1 --network testnet`\
`stellar keys address developer1`\
obtenemos la siguiente llave pública :\


_GCETVOMJKZ5OPTBIWBADL2PT6DTL7VPZMS5P4MIAYSINZYH5IIZRE27U_

Ahora que tenemos todos los datos ejecutamos:

```
stellar contract invoke --id CDLZFC3SYJYDZT7K67VZ75HPJVIEUVNIXF47ZG2FB2RMQQVU2HHGCYSC 
--network testnet --source-account developer -- transfer --from developer --to GD52UMJ54UXIGEGYT6GYRM4FAVATV7JD3RGEX6QGTV5HY66G5Q4T6RUS --amount 1000000000
```



Obtenemos lo siguiente:

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>
