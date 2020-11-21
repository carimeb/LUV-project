# LuV - Light UV Project 
_A portable IoT device that sends a Telegram message informing the measured UV index and is connected to a database in [ThingSpeak](https://thingspeak.com/). That's my final project to the course 'Fundamentals of IoT' in University of Sao Paulo._

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

Essa foto mostra meu dispositivo final:
<img src="https://github.com/carimeb/LUVproject/blob/main/images/LUVIMAGE.jpeg" width="1032" height="502">


### **Componentes**:
1. Módulo de Wifi Esp8266 Nodemcu V3 Lolin Com Chip Ch340g, como esse das fotos do MercadoLivre.com:
<p float="center">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/PLACA1.png" width="150" height="100">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/PLACA2.png" width="100" height="100">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/PLACA3.png" width="150" height="100">
</p>

2. Módulo de Sensor de Luz Ultravioleta Gy-VEML6070 com interface I2C, como esse das fotos do MercadoLivre.com:
<p float="center">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/SENSOR1.png" width="100" height="100">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/SENSOR2.png" width="100" height="100">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/SENSOR3.png" width="200" height="100">
</p>
  
3. Protoboard
4. 4 jumpers
5. Cabo USB x Micro USB
6. Fonte de bateria 5V como, por exemplo, um powerbank

Todos encontrados com facilidade em sites de eletrônicos ou de lojas anunciantes.


### **Instruções**:

#### 1° passo - Instalação de softwares

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
* [ArduinoJson.h](https://arduinojson.org/), para que a placa possa lidar com arquivos .json. **IMPORTANTE:** Usar a versão 6.x
* [UniversalTelegramBot.h](https://www.arduino.cc/reference/en/libraries/universaltelegrambot/), cliente do Telegram para o bot


#### 2° passo - Configuração do projeto no PlatformIO

Antes de tudo, para que seu sistema operacional Linux autorize a troca de dados com a entrada USB, caso ele já não estiver autorizando, é necessário rodar o seguinte comando no modo sudo (não se esqueça de trocar "username" pelo seu usuário no computador!):
```
usermod -a -G dialout username
```
É recomendável reinicializar o computador antes de continuar. Nem sempre as configurações são atualizadas somente fechando/abrindo o editor de texto.  Em seguida, crie um novo projeto no PlatformIO especificando qual placa (módulo) será usada em seu dispositivo, o framework e a platform. No meu caso, especifiquei, respectivamente: nodemcuv2 (ou nodemcu 1.0), arduino e espressif8266. 
Com isso, na pasta src já estará um arquivo main.cpp pronto para ser codado, com as bibliotecas básicas do Arduino já inclusas. Na pasta lib deverão ser instaladas as bibliotecas já mencionadas no 1° passo. 


#### 3° passo - Preparação do dispositivo

Na protoboard, conecte o módulo de wi-fi na fileira de sua preferência, de modo que a coluna dos pontos D0, D1, D2 etc fique apta a ser conectada com os jumpers. 
Na foto abaixo, ele está conectado na primeira casinha da fileira J e os jumpers estão conectados na fileira H, da seguinte forma: o jumper roxo conecta D1 (GPIO5) da placa com o SDA do sensor, o jumper cinza conecta o D2 (GPIO4) da placa com o SCL do sensor, o jumper verde conecta o G (GROUND) da placa com o GND do sensor e, por fim, o jumper vermelho conecta o 3V da placa com o VCC do sensor.

<p float="center">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/PROTOBOARD.png" width="300" height="400">
<img src="https://github.com/carimeb/LUVproject/blob/main/images/ILUSTRACAO.png" width="100" height="100">
</p>


#### 4° passo - Algumas explicações sobre o código em C++ (do arquivo main.cpp)

Dois pequenos lembretes: para compilar seu código no PlatformIO vá em Project Tasks e double-click em Build. Se der tudo certo, conecte a placa no seu computador (usando o cabo USB) e double-click em Upload para que o código binário seja enviado para a placa (eu demorei pra sacar essas coisas!). 

Voltando ao código, a velocidade da comunicação serial entre o computador e a placa foi definida em 115200, pois foi o valor mais comumente visto em códigos semelhantes. Observação: este valor deve ser igual ao configurado no PlatformIO em Serial Monitor. 
```C++
#define SERIAL_VELOCITY 115200
```

Ainda sobre a definição de valores, essas variáveis foram criadas e configuradas em números baixos com o objetivo de evitar ataques à segurança, como um alto número de requisições à subscrição de usuários ou _flooding_. Assim, uma pessoa pode enviar ao bot uma mensagem de no máximo 100 caracteres.
```C++
#define MAXLEN 100
#define MAXSUB 10
```

A função abaixo foi escrita após a leitura do seguinte tutorial para codar com a API do ThingSpeak usando a placa ESP8266: https://www.filipeflop.com/blog/esp8266-com-thingspeak/. Ela é bem simples e tem como objetivo configurar os canais de comunicação com o ThingSpeak. 
```C++
void write_thingspeak(int uvIndex) {
    if (client.connect(addr_api_thingspeak, 80)) {
        char buffer[100] = {0};
        sprintf(buffer,"field1=%d", uvIndex);
        client.print("POST /update HTTP/1.1\n");
        client.print("Host: api.thingspeak.com\n");
        client.print("Connection: close\n");
        client.print("X-THINGSPEAKAPIKEY: "+thingspeakKey+"\n");
        client.print("Content-Type: application/x-www-form-urlencoded\n");
        client.print("Content-Length: ");
        client.print(strlen(buffer));
        client.print("\n\n");
        client.print(buffer);
        last_connection_time = millis();                 //Time control, to know when send the next info to database 
        Serial.println("- Infos sent to ThingSpeak!");
    }
}
```

A função abaixo foi escrita após a leitura do seguinte tutorial: https://community.particle.io/t/getting-utc-time-from-ntp-server/1213. Ela é bem padrão também, usada por um cliente NTC para pegar o horário do fuso horário escolhido na internet.
```C++
void startTimeNclients() {
  timeClient.begin();
  configTime(0, 0, "pool.ntp.org");
  time_t now = time(nullptr);
  Serial.println(" - Sync...");
  while (now < 24 * 3600) {
    if(DEBUG) Serial.print(".");
    delay(100);
    now = time(nullptr);
  }
  timeClient.update();
}
```

Outro tutorial que me ajudou bastante a configurar a comunicação do protocolo I2C, do sensor UV, foi o seguinte: https://learn.sparkfun.com/tutorials/i2c/all. Além disso, foi indispensável o uso do datasheet do sensor para ver a escala do UV e programá-la direitinho depois da leitura feita pelo sensor. Pode ser baixada nesse link: https://www.vishay.com/docs/84310/designingveml6070.pdf.

<img src="https://github.com/carimeb/LUVproject/blob/main/images/uvIndexSheet.png" width="700" height="150">

Um ponto que eu mudaria num possível próximo código seria a forma de checar se o sistema está se repetindo ou não ao ser religado. Para que não haja menssagens em excesso, a checagem do momento de envio da última menssagem poderia ter sido feito usando datas ao invés de número para os dias da semana (0 - domingo, 6 - sábado). O trecho que me refiro é esse: 
```C++
void sendWarning() {
.
.

/*Correction for cycling count of weekend days*/
  if((timeClient.getDay()+1)%6 == lastToday) lastToday = timeClient.getDay();
  
 }
 
 #FicaDica! ;)
 ```
