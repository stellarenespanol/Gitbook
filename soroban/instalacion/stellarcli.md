# ☄️ Cliente Stellar

El cliente de Stellar es super importante en la escritura de contratos en Stellar, ya que se encarga de la creación de la estructura básica y archivos, su compilación, despliegue y pruebas respectivas.



### Intalación:

**MAC**&#x20;

Se ejecuta el siguiente comando en la terminal

```bash
brew install stellar-cli
```

#### Linux

Vamos a la pagina de releases [https://github.com/stellar/stellar-cli/releases/](https://github.com/stellar/stellar-cli/releases/)

{% hint style="info" %}
**busca el binario corresponidente a la  arquitectura de tu ordenador \[arch64 o x86\_64]**
{% endhint %}



<pre class="language-bash"><code class="lang-bash"># Caso de x86_64
wget https://github.com/stellar/stellar-cli/releases/download/v22.6.0/stellar-cli-22.6.0-x86_64-unknown-linux-gnu.tar.gz

# Caso de arch64

wget https://github.com/stellar/stellar-cli/releases/download/v22.6.0/stellar-cli-22.6.0-aarch64-unknown-linux-gnu.tar.gz 
<strong>
</strong></code></pre>

Descomprime el archivo&#x20;

```bash
#caso x86_64
tar -xvf stellar-cli-22.6.0-x86_64-unknown-linux-gnu.tar.gz

# Caso de arch64
tar -xvf stellar-cli-22.6.0-aarch64-unknown-linux-gnu.tar.gz
```

Crea un alias con el binario para que se ejecute en terminal con el comando stellar

```bash
sudo mv stellar /usr/local/bin/ && sudo chmod +x /usr/local/bin/stellar
```

Comprueba que se instaló correctamente

```bash
stellar --version
```

***

**⚠️ Nota si se usa un buntu 24.XXX ejecutar las siguientes instrucciones**

```bash
sudo nano /etc/apt/sources.list.d/jammy.list
```

dentro del editor de texto agegar la siguiente linea de código

```
deb [arch=amd64] http://archive.ubuntu.com/ubuntu jammy main
```

Se guarda los cambios (Ctrl+O) y cierra (Ctrl+X)

Ejecuta:

```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install build-essential libssl3 libstdc++6
```

***

**Windows**

```powershell
winget install --id Stellar.StellarCLI 
```

Para los diferentes sistemas operativos también se puede ir al repositorio de github y bajar de allí los últimos ejecutables

[https://github.com/stellar/stellar-cli/releases/](https://github.com/stellar/stellar-cli/releases/)

Una vez instalado en la terminal ejecutamos el comando stellar ( en la terminal).

```
stellar
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption><p>Ejecución de prueba</p></figcaption></figure>
