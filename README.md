# ttn-lorawan-application

Tutorial para a criação de um Device (*endpoint) utilizando o protocolo LoRaWAN com um Arduino Nano juntamente com uma aplicação na The Things Network e dashboard para acompanhamento em Node-RED.

O tutorial propõe mostrar em um dashboard (local) a infromação de temperatura capturada através de um sensor acoplado ao dispositivo (Arduino Nano) utilizando associado a rede The Things Network.

![cenario](https://github.com/mftutui/ttn-first-steps/blob/master/images/cenario.png)

Esse tutorial está dividido em 5 partes:

- Device: montagem
- Aplicação: criação
- Device: Arduino IDE
- Aplicação: payload
- Node-RED: dashboard

## Device: montagem

Reconhecimento dos componentes e montagem do sensor de temperatura.

O kit de desenvolvimento é composto por:
- [Arduino Nano(https://www.mouser.com/pdfdocs/Gravitech_Arduino_Nano3_0.pdf) 
- [Módulo transceptor LoRa RFM95](https://cdn.sparkfun.com/assets/learn_tutorials/8/0/4/RFM95_96_97_98W.pdf)
- Regulador de tensão
- Sensor de temperatura
- Protoboard
- Sensor NTC
- Resistor 10k
- Jumpers

A montagem deve ser feita da seguinte maneira:

![montagem](https://github.com/mftutui/ttn-first-steps/blob/master/images/montagem.png)

## Aplicação: criação

Criação da aplicação na TTN e associação de um dispositivo a mesma.

A partir do login feito na plataforma The Things Network escolha a opção *Console*. 

![ttn2](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn2.png)

Logo em seguida, *APPLICATIONS*. 

![ttn3](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn3.png)

Adicione uma aplicação.

![ttn4](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn4.png)

A aplicação deve ter:
- Application ID: Identificação para a sua aplicação.
- Description: Descrição da aplicação (não obrigatório).
- Handler registration: De acordo com a TTN é recomendado escolher o cluster de rede mais próximo dos gateways e colocar seu servidor de aplicativos próximo ao cluster. Isso minimiza a latência da rede. Os clusters com prefixos “ttn” são operados pela The Things Network Foundation, outros são operados por parceiros.

![ttn30](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn30.png)

Após criar a aplicação é necessário registrar um *Device*.

![ttn31](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn31.png)

Adicione o Device clicando em **register device**.

Complete o Device ID com uma identificação única para ele dentro da aplicação. 

O campo de **Device EUI** pode ser gerado e é responsável pela identificação do dispositivo na rede da TTN. Para gerá-lo basta clicar no box com as setas cruzadas à esquerda (destacado em vermelho).

![ttn10](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn10.png)

Ao final, clique em *Register*.

Uma tela como a seguinte deve aparecer:

![ttn11](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn11.png)


## Device: Arduino IDE

Gravação do código de rotina para a captura do valor de temperatura e envio do mesmo via *UpLink* para a aplicação criada na TTN.

Para a a gravação do código no Arduino será necessária a utilização da IDE do mesmo.

**1) Baixe e instale a IDE**

- [Arduino para Windows](https://www.arduino.cc/en/guide/windows)
- [Arduino para Linux](https://www.arduino.cc/en/guide/linux)
- [Arduino para macOS](https://www.arduino.cc/en/guide/macOSX)

***Driver* para Arduino Nano v3.0**

Para utilizar a placa Arduino Nano v3.0 Gravitech.us é necessário instalar o *Driver CH340/CH341USB*, ele se contra [nesse link](http://www.wch.cn/downloads/CH341SER_ZIP.html). 

Após a instalação do diver é necessário reiniciar o computador. 

***Inclusão das bibliotecas na IDE**

A programação do Arduino para o uso do *LoRaWAN* será feita a partir da bliblioteca *LMIC*. A biblioteca original pode ser encontrada [aqui](https://www.arduinolibraries.info/libraries/mcci-lo-ra-wan-lmic-library) e [aqui](https://github.com/mcci-catena/arduino-lmic). Mas, para facilitar, todas as configurações necessárias para a utilização da biblioteca no plano de frequência **AU915** já foram feitas e a biblioteca modificada está nesse repositório com o nome *[MCCI_LoRaWAN_LMIC_library-3.0.99(modificada).zip](https://github.com/mftutui/ttn-lorawan-application/blob/master/MCCI_LoRaWAN_LMIC_library-3.0.99(modificada).zip)*. 

A biblioteca *Thermistor* também será utilizada no código, ela está [aqui](https://github.com/mftutui/ttn-lorawan-application/blob/master/Thermistor.zip).

Após baixar, faça a adição das duas bibliotecas na IDE como mostrado na figura abaixo:

![arduino21](https://github.com/mftutui/ttn-first-steps/blob/master/images/arduino21.png)

Junto à biblioteca *LMIC* foi inserido um código base como exemplo para o uso e envio dos dados do sensor.

Ele se chama *hackathon-ttn-temperatura* pode ser encontrado em:

![arduino22](https://github.com/mftutui/ttn-first-steps/blob/master/images/arduino22.png)

***Inclusão das chaves **

No código estão faltando as chaves para associação do disposivivo e aplicação. Essas chaves contram-se diretamente na TTN.

As constantes devem ser preenchidas nos campos **FILMEIN** que estão entre as linhas 45 a 60.

```
// This EUI must be in little-endian format, so least-significant-byte
// first. When copying an EUI from ttnctl output, this means to reverse
// the bytes. For TTN issued EUIs the last bytes should be 0xD5, 0xB3,
// 0x70.
static const u1_t PROGMEM APPEUI[8] = { FILLMEIN };
void os_getArtEui (u1_t* buf) { memcpy_P(buf, APPEUI, 8);}

// This should also be in little endian format, see above.
static const u1_t PROGMEM DEVEUI[8] = { FILLMEIN };
void os_getDevEui (u1_t* buf) { memcpy_P(buf, DEVEUI, 8);}

// This key should be in big endian format (or, since it is not really a
// number but a block of memory, endianness does not really apply). In
// practice, a key taken from ttnctl can be copied as-is.
static const u1_t PROGMEM APPKEY[16] = { FILLMEIN };
void os_getDevKey (u1_t* buf) {  memcpy_P(buf, APPKEY, 16);}
```
A primeira constante a ser a ser preenchida **DEVEUI** pode ser encontrada, assim como as demais, na página do dispositivo. 

![ttn13](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn13.png)

Como mostrado na figura, é necessário clicar no campo destacado em vermelho para que o **DEVEUI** use o prefixo *0x* e os valores estejam separados por vírgulas.

Em seguida, esses valores deverão ser trocados de *msb* para *lsb* clicando no campo destacado em vermelho como pode ser visto na figura abaixo. 

![ttn15](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn15.png)

Para copiar o **Device EUI** basta clicar no campo a direita mostrado na figura seguinte e colá-lo no código.

![ttn16](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn16.png)


### Obs: demais alterações

- Intervalo de envio: tempo entre os envios de uplink
- Pin mapping: mapeamento dos pinos do Arduino para uso de SPI com o módulo RF96.
- Função **do_send()**: função responsável por fazer o envio dos dados (*uplink*).

Lembrando que é necessário configurar a placa, processador e porta.
- Placa: Arduino Nano
- Processador: "ATmega328P (Old Bootloader)"

Grave o código no dispositivos e acompanhe a saída serial para mais informações sobre a transmissão. 

Assim que reconhecido, o status do device muda na TTN.

![ttn22](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn22.png)

Na aba **Data** é possível oservar os dados chegando.

![ttn33](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn33.png)

### Aplicação: payload

Apesar de conseguir observar a chegada dos dados ainda não é possível identificá-los como valores de temperatura.

Para isso, será feita a decodificação do payload. Em **Payload Formats** é possível inserir um código (em javaScript) capaz de transformar os valores recebidos para que os mesmos se tornem de fácil identificação.

```
function Decoder(bytes, port) {
  var value1 = bytes[0];

  return {
    Temperatura: value1+"°C"
  };
}
```

![ttn26](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn26.png)

O resultado da decodificação do payload pode ser observado também na aba **Data**, agora apresentada da seguinte maneira: 

![ttn28](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn28.png)

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

![nod-red1](https://github.com/mftutui/ttn-first-steps/blob/master/images/nod-red1.png)

Vá até *Manage palette*:

![node-red2](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red2.png)

Em *Install* procure por *node-red-dashboard* e faça a instalação do módulo:

![ndoe-red4](https://github.com/mftutui/ttn-first-steps/blob/master/images/ndoe-red4.png)

Em seguida, repita o procedimento para o módulo *node-red-contrib-ttn*. 

![node-red5](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red5.png)

Após a instação novas caixinhas de *nodes* aparecerão na barra lateral esquerda:

![animation](https://github.com/mftutui/ttn-first-steps/blob/master/images/animation.gif)

Mais umas vez clique nos três risquinhos e procure por *Import*. 

![node-red19](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red19.png)

Nesse caso vamos importar um flow inteito, para facilitar. O arquivo do flow está [aqui](https://github.com/mftutui/ttn-lorawan-application/tree/master/node-red).

![node-red21](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red21.png)

Logo após, o flow poderá ser visto e deve parecer com esse:

![node-red23](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red23.png)

Ao clicar duas vezes no bloco *Sensor* a opção de configuração do mesmo será mostrada. Abra o app chamado de ttn e o modifique inserindo o *App ID* e *Access Key*.

![node-red29](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red29.png)

O *App ID* ou *Application ID* é simplesmente o ID dado para a aplicação na hora da criação.

![ttn39](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn39.png)

A chave de *Access Keys* contra-se ao final da página da aplicação. 

![ttn40](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn40.png)

Essas informações podem ser encontradas na aplicação criada na TTN.

![node-red30](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red30.png)

Após preenchidas é necessário ainda inserir o ID do Device, contrado não na aplicação mas no próprio Device.

![ttn43](https://github.com/mftutui/ttn-first-steps/blob/master/images/ttn43.png)

Finalmente a aplicação não precisa de mais configurações.

![node-red35](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red35.png)

O acesso para o dashboard pode ser feito pelo endereço: **http://127.0.0.1:1880/ui**, nesse exemplo ele ficou assim:

![node-red37](https://github.com/mftutui/ttn-first-steps/blob/master/images/node-red37.png)





