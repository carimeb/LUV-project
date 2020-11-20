# LuV - Light UV Project
_A portable IoT device that sends a Telegram message informing the measured UV index and is connected to a database in [ThingSpeak](https://thingspeak.com/)._

A incidência de raios ultravioletas (UV) na cidade de Sao Paulo é alta durante quase todo o ano. Até mesmo no inverno seus níveis elevados  
surpreendem os desavisados ou quem esqueceu de checar a previsão do tempo. Como sabemos, sem o uso de protetor solar as queimaduras causadas 
pela longa exposição levam à desidratação e, a longo prazo, podem causar câncer. Além disso, em estufas má condicionadas podem causar danos às
culturas frágeis ou prejudicar animais de criação expostos ao ar livre em dias que previu-se baixo índice UV, porém houve o contrário.

Para evitar esses efeitos colaterais, esse dispositivo mede a incidência de luz UV na localidade de interesse e envia o índice medido via 
Telegram de duas maneiras: no momento de uma solicitação, através do comando "/UV" ou via assinatura, no qual o índice é medido de hora em hora 
e salvo em uma base de dados no ThingSpeak. Lá, ele é comparado com o índice UV informado pelo serviço de previsão do tempo e, se houver diferença,
é enviado um alerta de diferença de medições. Assim, podemos medir a eficiência do índice UV informado por determinado serviço de previsão de tempo
que muitas vezes é pago por grandes produções agrícolas ou fazendas criadoras de animais em regiões remotas do país, mas não tem como avaliá-lo. 
E também nos lembrar de passar o protetor solar! ;-)

![Image of LuV](https://github.com/carimeb/LUVproject/blob/main/LUVIMAGE.jpeg)


### **Componentes**:
1. Módulo de Wifi Esp8266 Nodemcu V3 Lolin Com Chip Ch340g
2. Módulo de Sensor de Luz Ultravioleta I2c Gy-VemL6070
3. Protoboard
4. 4 fios coloridos
5. Cabo USB x Mini USB 

Todos encontrados com facilidade em sites de eletrônicos ou lojas anunciantes.


### **Instruções**:


