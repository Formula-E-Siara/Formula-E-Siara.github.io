---
title: "Escolha de sensores"
tags:
- elétrica
- telemetria
weight: -5
date: "2023-01-03"
---

OK
- IMU MPU 9250, um nível acima do mais básico, MPU6050
- GPS Ublox neo 6m / m8n
- RPM: simples encoder óptico(incremental), pode até ser DIY impresso 3D, no caso mais simples de acoplar sendo só um disco que vai ser lido pelo encoder de quadratura. Senão temos duas opções, usar um encoder óptico rotativo direto na roda/eixo ou com uma redução com uma constante alterada em software.
- Transmissão de dados: par de ESP32 com anteninha ou um microcontrolador com wifi mais forte.

BOM
- IMU: Bosch BNO055. Justificativa → É um pouco melhor que o 9250, mas aparentemente incrível. O BNO tem um algoritmo de fusão sensorial que pode ajudar com a parte do GPS, removendo peso do controlador principal. Ganha em alguns aspectos do 9250
- GPS: SparkFun GPS-RTK2 Board - ZED-F9P → 0,05 m/s, dobro do ultimo, ODR de 8 a 25 Hz, dependendo do modo. Aparentemente 1,5m de erro, não fica muito claro na datasheet. Tem dois tipos de medidas o PVT, aparentemente o padrão de todos, de onde eu tirei os 1,5m. Também tem o RTK, que pode ser preciso por até 10cm, supostamente segundo a datasheet, mas esse método é mais preciso mesmo. Precisa importar.
- Sensor indutivo industrial, simples, a ser acoplado na engrenagem do eixo da roda, o que varia vai ser a frequência e distância de ativação.
- Transmissão de dados: ZigBee/LoRa

Excelente
- IMU, GPS, barômetro: VectorNav VN-300, é um INU, intertial navigation system. Possui todas as funções, é só um dos modelos usados para máquinas pesadas de campo, ou mesmo navios, é um grande exagero, contudo, é mais que excelente.
- RPM: sensor indutivo comercial especificamente para carros.
- Transmissão de dados: HolyBro SiK Telemetry Radio V3, similar ao do PixHawk4

Transmissão de dados
Algumas situações são possíveis, considerando que serão transmitidos os dados dos sensores apenas, sem câmeras ou LIDAR, devido ao grande volume de dados, importando mais o controle e condições de operação do carro
- para um setup mais simples, um ESP32 ou mesmo o Wi-Fi do Raspberry Pi podem ser suficientes, unidos de um bom roteador, sendo possível andar um pouco apenas para tester simples. 
- Outra opção é usar um módulo 4G no Raspberry Pi, no entanto acrescenta complexidade. 
- Há módulos de rádio LoRa que alcançam quilometros de distância e possuem protocolo robusto, o LoRaWAN, basicamente o módulo é uma camada, para os bytes brutos, a comunicação real seria com MQTT ou TCP/IP.
- O ideal é um módulo de rádio como que os drones possuem, no entanto o preço é alto.
