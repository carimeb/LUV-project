# LuV - Light UV Project 
_A portable IoT device that sends a Telegram message informing the measured UV index and is connected to a database in [ThingSpeak](https://thingspeak.com/). That's my final project to 'Fundamentals of IoT' course in University of Sao Paulo._

A incidência de raios ultravioletas (UV) na cidade de Sao Paulo é alta durante quase todo o ano. Até mesmo no inverno seus níveis elevados  
surpreendem os desavisados ou quem esqueceu de checar a previsão do tempo. Como sabemos, sem o uso de protetor solar as queimaduras causadas 
pela longa exposição levam à desidratação e, a longo prazo, podem causar câncer. Além disso, em estufas má condicionadas podem causar danos às
culturas frágeis ou prejudicar animais de criação expostos ao ar livre em dias que previu-se baixo índice UV, porém houve o contrário.

Para evitar esses efeitos colaterais, esse dispositivo mede a incidência de luz UV na localidade de interesse e envia o índice medido via 
Telegram de duas maneiras: no momento de uma solicitação, através do comando "uv" ou via assinatura, no qual o índice é medido de hora em hora 
e salvo em uma base de dados no ThingSpeak. Lá, ele é comparado com o índice UV informado pelo serviço de previsão do tempo e, se houver diferença,
é enviado um alerta de diferença de medições. Assim, podemos medir a eficiência do índice UV informado por determinado serviço de previsão de tempo
que muitas vezes é pago por grandes produções agrícolas ou fazendas criadoras de animais em regiões remotas do país, mas não tem como avaliá-lo. 
E também nos lembrar de passar o protetor solar! ;-)


<img src="https://github.com/carimeb/LUVproject/blob/main/images/LUVIMAGE.jpeg" width="332" height="602">


### **Componentes**:
1. Módulo de Wifi Esp8266 Nodemcu V3 Lolin Com Chip Ch340g, como essa das fotos:
<p float="center">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/PLACA1.png" width="150" height="100">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/PLACA2.png" width="100" height="100">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/PLACA3.png" width="150" height="100">
  </p>

2. Módulo de Sensor de Luz Ultravioleta Gy-VEML6070 com interface I2C, como esse das fotos:
<p float="center">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/SENSOR1.png" width="100" height="100">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/SENSOR2.png" width="100" height="100">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/SENSOR3.png" width="200" height="100">
  </p>
  
3. Protoboard
4. 4 jumpers
5. Cabo USB x Micro USB
6. Fonte de bateria 5V como, por exemplo, um powerbank

Todos encontrados com facilidade em sites de eletrônicos ou lojas anunciantes.


### **Instruções**:

#### 1° passo - Instalação dos softwares

Certifique-se de ter instalado em seu editor de texto o IDE PlatformIO, uma plataforma profissional colaborativa para desenvolvimento de sistemas embarcados. Usaremos as bibliotecas abaixo que, por padrão, já estão instaladas nele:

* Arduino.h, a biblioteca default do Arduino usada também pela placa Nodencu
* ESP8266WiFi.h, para o wi-fi da placa
* ESP8266mDNS.h, para troca de mensagens DNS
* ESP8266HTTPClient.h
* WiFiClientSecure.h
* WiFiUdp.h, para troca de mensagens UDP
* Wire.h, I2C
* LittleFS.h

Entretanto, essas daqui precisarão ser instaladas:

* [Adafruit_VEML6070.h](https://github.com/adafruit/Adafruit_VEML6070), para o sensor UV
* [NTPClient.h](https://www.arduino.cc/reference/en/libraries/ntpclient/), para conectar a um servidor NTP
* [ArduinoJson.h](https://arduinojson.org/), para que a placa possa lidar com arquivos .json
* [UniversalTelegramBot.h](https://www.arduino.cc/reference/en/libraries/universaltelegrambot/), cliente do Telegram para o bot


#### 2° passo - Preparação da placa

Na protoboard, conecte o módulo de wi-fi na fileira de sua preferência, de modo que a coluna dos pontos D0, D1, D2 etc fique apta a ser conectada com os jumpers. 
Na foto abaixo, ele está conectado na primeira casinha da fileira J e os jumpers na fileira H, da seguinte forma:

<img src="https://github.com/carimeb/LUVproject/blob/main/images/PROTOBOARD.jpeg" width="332" height="602">



```C++
void connect() {
  if(DEBUG) Serial.println("Try to connect in WiFi network...");
  while (WiFi.status() != WL_CONNECTED) {
      delay(500);
      if(DEBUG) Serial.print(".");
  }
}
```
