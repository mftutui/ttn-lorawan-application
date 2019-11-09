# ttn-lorawan-application

Tutorial para a criação de um Device utilizando o protocolo LoRaWAN com um Arduino Nano, aplicação na The Things Network e dashboard em Node-RED.

A aplicação propõe mostrar um dashboard em um computador a infromação de temperatura capturada através de um sensor acoplado a um dispositivo.

[cenario](https://github.com/mftutui/ttn-first-steps/blob/master/images/cenario.png)

Esse tutorial está dividido em 5 partes sendo elas:

- Device: montagem
- Aplicação: criação
- Device: Arduino IDE
- Aplicação: payload
- Node-RED: dashboard


### Device: montagem

Reconhecimento dos componentes e montagem do sensor de temperatura.

O kit de desenvolvimento é composto de:
- Arduino Nano
- Módulo transceptor LoRa RFM95
- Regulador de tensão
- Sensor de temperatura
- Protoboard
- Sensor NTC
- Resistor 10k
- Jumpers

A montagem deve ser feita da seguinte maneira:

[](https://github.com/mftutui/ttn-first-steps/blob/master/images/montagem.png)

### Aplicação: criação

Criação da aplicação na TTN e associação de um dispositivo a mesma.

A partir do login feito na plataforma The Things Network escolha a opção *Console*. 

[ttn2](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn2.png)

Logo em seguida, *APPLICATIONS*. 

[ttn3](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn3.png)

Adicione uma aplicação.

[ttn4](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn4.png)

A aplicação deve ter:
- Application ID: Identificação para a sua aplicação.
- Description: Descrição da aplicação (não obrigatório).
- Handler registration: De acordo com a TTN é recomendado escolher o cluster de rede mais próximo dos gateways e colocar seu servidor de aplicativos próximo ao cluster. Isso minimiza a latência da rede. Os clusters com prefixos “ttn” são operados pela The Things Network Foundation, outros são operados por parceiros.

Adicione o Device clicando em **register device**.

Complete o Device ID com uma identificação única para o mesmo dentro da aplicação. O campo de **Device EUI** pode ser completado pela TTN e é responsável pela identificação do dispositivo na rede da TTN.

### Device: Arduino IDE

Serão necessárias as instalações da IDE do Arduino e também do *driver* específico para usar juntamente com o Arduino Nano disponibilizado. 

**1) Baixe e instale a IDE**

- [Arduino para Windows](https://www.arduino.cc/en/guide/windows)
- [Arduino para Linux](https://www.arduino.cc/en/guide/linux)
- [Arduino para macOS](https://www.arduino.cc/en/guide/macOSX)

**Pacote para Arduino Nano**

Para utilizar a placa Arduino Nano v3.0 Gravitech.us é necessário instalar o [*Driver CH340/CH341USB*](http://www.wch.cn/downloads/CH341SER_ZIP.html).

![node-red7](https://github.com/mftutui/tutorial-lora-application/blob/master/imagens-tutorial/node-red7.png)

Após a instalação do diver é necessário reiniciar o computador. 

A biblioteca LMIC modificada especialmente para o uso da frequêcia em AU915 eocontra-se em ********************.

Faça a adição da biblioteca na IDE como mostrado na figura abaixo:

[arduino21](https://github.com/mftutui/ttn-first-steps/blob/master/images/arduino21.png)

Junto à biblioteca foi inserido um código base para o uso e envio dos dados do sensor.

Esse código pode ser encontrado em:

[arduino22](https://github.com/mftutui/ttn-first-steps/blob/master/images/arduino22.png)

Não esqueça que no código estão faltando as chaves para associação do disposivivo e aplicação. Essas contram-se diretamente na TTN.

#### Obs: demais alterações

- Intervalo de envio: tempo entre os envios de uplink
- Pin mapping: mapeamento dos pinos do Arduino para uso de SPI com o módulo RF96.
- Função **do_send()**: funçAo responsável por fazer o envio dos dados.

Lembrando que é necessário configurar a placa, processador e porta.
- Placa: Arduino Nano
- Processador: "ATmega328P (Old Bootloader)"

Grave o código no dispositivos e acompanhe a saída serial para mais informações sobre a transmissão. 

Assim que reconhecido, o status do device muda na TTN.

[ttn22](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn22.png)

Na aba **Data** é possível oservar os dados chegando.

[ttn33](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn33.png)

### Aplicação: payload

Apesar de conseguir observar a chegada dos dados ainda não é possível identificá-los como valores de temperatura.

Para isso, será feita a decodificação do payload. Em **Payload Formats** é possível inserir um código (em javaScript) capaz de transformar os valores recebidos para que os mesmos se tornem de fácil identificação. 

[ttn26](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn26.png)

O resultado da decodificação do payload pode ser observado também na aba **Data**, agora apresentada da seguinte maneira: 

[ttn28](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn28.png)

### Node-RED: dashboard

O uso do dashboard implica na instalação do *Node.js* e também do *Node-RED*

**1) Instalar *Node.js***

No site oficial do [Node.js](https://nodejs.org/en/download/) faça o download da versão do instalador adequada para o seu sistema operacional. A instalação deve ser feita por um usuário com acesso *root* na máquina. 

Após concluír a instalação execute no terminal os comandos abaixo para revificar se a mesma foi feita corretamente:

```
> node --version 
> npm --verson
```

As respostas para os comandos devem ser parecidas com:

```
> v10.16.3
> 6.11.3
```

**2) Instalar o Node-RED**
  
A instalação do Node-RED pode ser feita via terminal a partir do comando:

```
> npm install -g --unsafe-perm node-red
```

- Iniciar o Node-RED

A inicialização do Node-RED é feita também no *terminal* pelo comando:

```
> node-red
```

Acesse a aplicação pelo navegador usando o endereço **http://127.0.0.1:1880/**

**3) Preparando o ambiente no Node-RED**
  
Dentro da plataforma clique nos três risquinhos no canto superior direito:

![node-red1](https://github.com/mftutui/tutorial-lora-application/blob/master/imagens-tutorial/node-red1.png)

Vá até *Manage palette*:

![node-red2](https://github.com/mftutui/tutorial-lora-application/blob/master/imagens-tutorial/node-red2.png)

Em *Install* procure por *node-red-dashboard* e faça a instalação do módulo:

![node-red4](https://github.com/mftutui/tutorial-lora-application/blob/master/imagens-tutorial/ndoe-red4.png)

Em seguida, repita o procedimento para o módulo *node-red-contrib-ttn* 

![node-red5](https://github.com/mftutui/tutorial-lora-application/blob/master/imagens-tutorial/node-red5.png)

Após a instação novas caixinhas de *nodes* aparecerão na barra lateral esquerda:

![app](https://github.com/mftutui/tutorial-lora-application/blob/master/imagens-tutorial/animation.gif)
