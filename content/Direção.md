---
title: "Direção"
tags:
- elétrica
- powertrain
weight: -5
date: "2023-01-03"
---

>[!tip] Não repare a bagunça, esse tópico ainda ta sendo organizado . . ...🐌

# 1. Acionamento motor por BT (HSS86)

## 1.1. Motor de passo

![](content/images/direcao_motor_driver.png)

O motor utilizado para o projeto da direção elétrica será o Tecmaf 34080 ([TMA.ES 34080](https://tecmaf.com.br/wp-content/uploads/2020/07/TMA.ES_.34080.pdf)), motor de passo bipolar com força de 8 Nm, tal que cada passo equivale a um ângulo de 1,8°± 5%. Algumas informações sobre o motor:

![](content/images/direcao_motor.png)

O acionamento do motor está sendo feito com o apoio do driver dedicado ([HSS86](https://www.jbcnc.se/images/datasheets/HSS86.pdf)). O driver permite muitas funcionalidades úteis, entre elas:

-       Controle em malha fechada do motor que não permite a perda de passo;
-       Melhoria na performance em alta velocidade e alto torque;
-       Ajuste automático de corrente baseado na carga de trabalho reduzindo o aquecimento;
-       Frequência de resposta de pulso de 200 kHz;
-       Possibilidade de escolha entre 16 micropassos (em escala de 400 a 51200 crosteps/rev) através das chaves físicas na lateral do driver.
-       Proteção contra sobrecorrente, sobretensão e diferença de posição.

### 1.1.1. Diagrama de conexão

![](content/images/direcao_esquema_hss86.png)

### 1.1.2. Fios encoder motor

![](content/images/direcao_db15_cores.png)

### 1.1.3. Conexão encoder com driver HSS86

![](content/images/direcao_conexao_encoder_hss86.png)

 
## 1.2. Hybrid Servo Driver (TMA DES HSS86)

### 1.2.1. Luz segurança

![](content/images/direcao_luz_seg_hss86.png)

### 1.2.2. Configuração dos switches

Usaremos configuração default. Para acionamento do motor não precisa tanta precisão

![](content/images/direcao_microstep.png)

ou seja, todas as chaves devem ficar em on

### 1.2.3. Controlador

>[!attention] Observação importante: Ligar o motor e driver com cabo desconectado poderá danificar o driver e o encoder

Estaremos usando o arduino que tem tensão de sinal de 5V logo não precisa dos resistores conforme tabela abaixo. Para teste com esp (próximo tópico), usaremos um optacoplador ligado aos pinos de saída, assim escolhemos a resistência de 2,2k pois usaremos 24V para dar maior margem para caso haja queda de tensão (checar diagrama 1.1.1.)

![](content/images/direcao_pinos_controle_hss86.png)

#### Pinos (HSS86)
-       ENA (Enable): HIGH habilita o motor (considerando acionamento em HIGH conforme diagrama do datasheet do HSS86)
-       DIR (Direction): HIGH horário/ LOW anti horário.
-       PUL (Pulse): Esta é a entrada de pulso do HSS86. Quando um pulso de sinal lógico alto é aplicado a essa entrada, o motor avança um passo ou uma posição. O número de pulsos necessários para um movimento completo depende das características do motor.

#### Código (ESP32)

````
const int pin_ena = 2; //habilita o motor

const int pin_dir = 3; //determina a direção

const int pin_pul = 4; //executa um passo

int periodo = 1000; // micro segundos

int step = 5; //Conta 400 pulsos

boolean pulso = LOW; //estado do pulso

/*Para saber a velocidade e o quanto o motor vai girar defina qual a configuração

do "divisor" driver, 400 pulso/rev, 800pulso/rev 1600pulso/rev e etc

A velocidade será freq/divisor, onde freq= 1/periodo

EX: divisor default=400; periodo= 500us; freq=1/periodo= 1kHz

Resulta em 1000(pulso/s)/400(pulso/rev)=2.5 rev/s e 2.5 rev/s = 150 rpm

- Se por exemplo step=400, sera executado 400 passos com o divisor fica

step/divisor=400(pulsos)/400(pulso/rev) que resulta em 1 rev.

- Se sao 400 pulsos com velocidade 2.5 rev/s o tempo de 1 volta será de 1rev/2.5 0.4 segundos

*/

void setup(){

  pinMode(pin_ena, OUTPUT);

  pinMode(pin_dir, OUTPUT);

  pinMode(pin_pul, OUTPUT);

  digitalWrite(pin_ena, HIGH); //habilita em high, ligando portas positivas em high

  digitalWrite(pin_dir, HIGH); // low ANTIHORARIO / high HORARIO

  digitalWrite(pin_pul, LOW); //borda de decida

}

void loop(){

  delay(1000);

  for(int i=0; i<step; i++){

    pulso = !pulso; //inverte o estado da variável

    digitalWrite(pin_pul, pulso); //atribui o novo estado à porta

    delayMicroseconds(periodo*1000/2); //tempo em alta do pulso

    pulso = !pulso; //inverte o estado da variável

    digitalWrite(pin_pul, pulso); //atribui o novo estado à porta

    delayMicroseconds(periodo*1000/2); //tempo em baixa do pulso

  }

}

````

#### Error

![](content/images/direcao_error.png)
## 1.3. Controlando por celular

Celular(bluetooth) → ESP → optoacoplador → driver HSS86

### 1.3.1. ESP (portas BT)

![](content/images/direcao_pinout_esp32.png)
### 1.3.2. (Saída ESP)

Necessário entre esp e driver pq saída esp é de 3,3V

#### Optacoplador TLP281-4

Prático 4 entradas -> 4 saídas
![](content/images/direcao_TLP281_4.png)

![](content/images/direcao_TLP281-4_esquema.png)

#### Conversor de Nível Lógico 3.3V-5V Bidirecional - 2 Canais

Sugestão do professor, mas não achamos no lab

![](content/images/direcao_conversor_nivel_logico.png)

#### Optacoplador HCPL-3120

tinhamos sobrando no lab

![](content/images/direcao_HCPL_3120.png)

>[!attention] É necessário uso de um capacitor de bypass de 0,1 μF entre os pinos 5 e 8

Por datasheet do opta, temos:

![](content/images/direcao_HCPL_3120_datasheet.png)

##### Input

A tensão de entrada será da ESP32 que é de 3,3V, então o resistor de entrada deve ser 330Ω (Ωmin=3,3V/16mA=206Ω)

##### Output

>[!attention] É necessário se atentar à necessidade de resistência entre o controlador e o driver de acordo com a tensão de controle utilizada.
Pelo datasheet, temos:
![](content/images/direcao_resistencia_hss86.png)

Usaremos alimentação de saída em 24V (tem q estar entre 15 e 30V e o datasheet do hss dá opção de 5V, 12V e 24V). Assim, lembrando pelo diagrama 1.1.1. e tabela 1.2.3. precisamos usar resistência de aproximamente 2k, usaremos 2k2 Ω

### 1.3.4. (Alimentação ESP)

A alimentação da esp pode ser feita pelo próprio conector USB (5,0V) ou então através do pino 5V ou VIN , com uma alimentação regulada de 5,0V

#### Regulador KA7805

![](content/images/direcao_KA7805.png)

![](content/images/direcao_KA7805_data1.png)

![](content/images/direcao_KA7805_data2.png)

### 1.3.5. Aplicativo para controlar pelo celular

#### Aplicativo 1: Só terminal Serial

![](content/images/direcao_app1.jpg)

![](content/images/direcao_app1_example.jpg)

##### Código acionamento (v1)

````
#include "BluetoothSerial.h"

const int ena = 33; //habilita o motor

const int dir = 32; //determina a direção

const int pul = 3; //executa um passo

String comando;

int periodo = 500; // em ms, faixa sugerida de 100 a 2000 (obs colocar numero par)

int step = 4000; //Conta x pulsos

boolean pulso = LOW; //estado inicial do pulso

int pul_rev;

int tempo;

int angulo;

int flag=0;

String letra;

BluetoothSerial SerialBT;

void setup() {

  Serial.begin(9600); // Inicializa a porta serial para depuração

  pinMode(ena, OUTPUT);

  pinMode(dir, OUTPUT);

  pinMode(pul, OUTPUT);

  digitalWrite(ena, HIGH); //habilita em high, ligando portas positivas em high

  digitalWrite(pul, LOW); //borda de decida

  SerialBT.begin("Controle remoto"); // Inicializa a comunicação Bluetooth

}

void loop() {

  delay(500);

  while(!flag){

    SerialBT.println("Informe valor pul_rev definido nas chaves do HSS86");

    delay(5000);

    if (SerialBT.available()) { // Verifica se há dados disponíveis na conexão Bluetooth

      comando=SerialBT.readStringUntil('\n');

      pul_rev=comando.toInt();

      SerialBT.println("Envie um comando");   

      flag=1;

    }

  }

  if (SerialBT.available()) { // Verifica se há dados disponíveis na conexão Bluetooth

    comando= SerialBT.readStringUntil('\n');  // Lê o comando enviado pelo dispositivo remoto

    comando.replace("\n", ""); // Remove o caractere '\n' da string

    letra=comando[0];

    comando.replace(letra, "");

    switch(letra[0]){     

      //Direção

      case 'D':

      case 'd':

        if(comando[0]=='0'){digitalWrite(dir, LOW); SerialBT.println("mudou dir pra 0");}

        else if(comando[1]=='1'){digitalWrite(dir, HIGH); SerialBT.println("mudou dir pra 1");   }   

        else{SerialBT.println("Comando inválido3");}     

        break;

      //Enable

      case 'E':   

      case 'e' :

        if(comando[0]=='0'){digitalWrite(ena, LOW);SerialBT.println("mudou ena pra 0");}

        else if(comando[1]=='1'){digitalWrite(ena, HIGH);SerialBT.println("mudou ena pra 1");}   

        else{SerialBT.println("Comando inválido1");}     

        break;

      //Periodo em ms, faixa sugerida de 100 a 2000 (obs colocar numero par)

      case 'P':

      case 'p':

        periodo=comando.toInt();

        SerialBT.println("periodo alterado");

        SerialBT.println(periodo);

        break;

      //Numero de passos (Steps)

      case 'S':

      case 's':   

        step=comando.toInt();

        SerialBT.println("step alterado");SerialBT.println(step);

        break;

      //Angulo de rotação em graus

      case 'A':

      case 'a':         

        angulo=comando.toInt(); 

        step=angulo*(pul_rev)/180;

        SerialBT.println("angulo alterado");SerialBT.println(angulo);

        break;

      //Tempo de acionamento em ms

      case 'T':

      case 't': 

        tempo=comando.toInt();

        SerialBT.println("tempo alterado");SerialBT.println(tempo);

        step=tempo*1000/periodo;

        break;

      default:

        SerialBT.println("Comando inválido2");

        break; 

      }

    }

  for(int i=0; i<=step; i++){

    digitalWrite(pul, pulso); //atribui o novo estado à porta

    delayMicroseconds(periodo/2); //tempo em alta do pulso

    pulso = !pulso; //inverte o estado da variável

    digitalWrite(pul, pulso); //atribui o novo estado à porta

    delayMicroseconds(periodo/2); //tempo em baixa do pulso

  }

}

````

##### Código acionamento (v2)

````
#include "BluetoothSerial.h"

BluetoothSerial SerialBT;

//Definindo pinos

const int pin_ena = 15;                        //habilita o motor

const int pin_dir = 5;                        //determina a direção

const int pin_pul = 4;                        //executa um passo

//Variaveis para leitura mensagem bt

String comando;

String letra;

//Variáveis para controle do motor

boolean direcao = HIGH;                       // low AH - high H

boolean enable = LOW;

volatile boolean pulso = LOW;                          //estado inicial do pulso (borda de decida)

volatile int step = 4000;                              //Conta x pulsos

volatile unsigned long periodo = 6;                              /* microsegundos (periodo minimo)

                                              Frequencia do driver 200kHz (ou seja, 1 pulso em 1/200k=0.005 ms=5us).

                                              O ideal seria 2.5 (positivo e negativo), mas vamos trabalhar com margem*/

//Variáveis para calculo/conversão

volatile int tempo;

int angulo;

int pul_rev;

//Flags intermediarias

int ang_neg=0;                                 //flag sinal angulo

int flag=0;                                    //flag valor pul_rev recebido

int stay=0;                                    //flag comando permanecer ligado

void setup(){

  Serial.begin(9600);                          // Inicializa a porta serial para depuração

  pinMode(pin_ena, OUTPUT);

  pinMode(pin_dir, OUTPUT);

  pinMode(pin_pul, OUTPUT);

  digitalWrite(pin_ena, enable);

  digitalWrite(pin_dir, direcao);

  digitalWrite(pin_pul, pulso);

  SerialBT.begin("Controle remoto"); // Nome do dispositivo para comunicação BT

}

void loop(){

  //Pro calculo de tempo e angulo precisa saber o valor de pul_rev (chaves do hss86)

  while(!flag){

    SerialBT.println("Informe valor pul_rev definido nas chaves do HSS86");

    delay(5000);

    if (SerialBT.available()) { //Verifica se há dados disponíveis na conexão Bluetooth

      comando=SerialBT.readStringUntil('\n');

      pul_rev=comando.toInt();

      switch(pul_rev){

        case 400:   case 800:   case 1000:  case 1600:

        case 2000:  case 3200:  case 4000:  case 5000:

        case 6400:  case 8000:  case 10000: case 12800:

        case 20000: case 25600: case 40000: case 51200:

            SerialBT.print("Pul_rev = ");  SerialBT.println(pul_rev);

            SerialBT.println("Envie um comando");

            flag=1;

        break;

        default:

          SerialBT.println("ERRO1. Obs: default=400");

          break;

      }

    }

  }

  if (SerialBT.available()) {                 // Verifica se há dados disponíveis na conexão Bluetooth

    //Tratar comando recebido

    comando= SerialBT.readStringUntil('\n');  // Lê o comando enviado pelo dispositivo remoto

    comando.replace("\n", "");                // Remove o caractere '\n' da string

    letra=comando[0];

    comando.replace(letra, "");

    switch(letra[0]){ 

      //Direção

      case 'D':

      case 'd':

        if(comando[0]=='0'){direcao=0;}

        else if(comando[0]=='1'){direcao=1;}   

        else{SerialBT.println("\nERRO2\nValor de direção invalida");}

        digitalWrite(pin_dir, direcao);

      break;

      //Enable

      case 'E':

      case 'e' :

        if(comando[0]=='0'){enable=0;}

        else if(comando[0]=='1'){enable=1;}   

        else{SerialBT.println("\nERRO3\nValor de enable invalido");}

        digitalWrite(pin_ena, enable);

      break;

      //Periodo em us

      case 'P':

      case 'p':

        periodo=comando.toInt();

      break;

      //Numero de passos (Steps)

      case 'S':

      case 's':

        if(comando[0]=='.'){SerialBT.println("Modo ligado até comando stop"); step=1; stay=1;}

        else{step=comando.toInt();}

      break;

      //Angulo de rotação em graus

      case 'A':

      case 'a':

        if(comando[0]=='-'){ang_neg=1; direcao=0; comando.replace("-", "");}

        else{ang_neg=0; direcao=1;}     

        digitalWrite(pin_dir, direcao);

        angulo=comando.toInt(); 

        step=angulo*(pul_rev)/360;

      break;

      /*

      Não consegui corrigir o tempo, quando envio o comando, a esp desconecta     

      //Tempo de acionamento em s

      case 'T':

      case 't': 

        tempo=comando.toInt();

        periodo=tempo*1000000/step;

      break;

      */

      default:

        SerialBT.println("\nERRO4");

      break;

    }

    if(periodo<5){periodo=6;SerialBT.println("\nERRO5\nOBS: Periodo mínimo = 5us (fmax=200kHz). Periodo setado para 6us");}

    SerialBT.printf("\nperiodo(us) = %i\nstep = %i\ndirecao = %i\nenable =  %i\n",periodo,step,direcao,enable);

    if(enable){

      SerialBT.println("\nIniciado");

      for(int i=0; i<step; i++){

        pulso = !pulso;                                    //inverte o estado da variável

        digitalWrite(pin_pul, pulso);                      //atribui o novo estado à porta

        delayMicroseconds(((   periodo/2  )*2)/2);         //tempo em alta do pulso (divide por 2 e multiplica por 2 pra ter certeza q vai ser inteiro)

        pulso = !pulso;                                    //inverte o estado da variável

        digitalWrite(pin_pul, pulso);                      //atribui o novo estado à porta

        delayMicroseconds(((   periodo/2  )*2)/2);         //tempo em baixa do pulso (divide por 2 e multiplica por 2 pra ter certeza q vai ser inteiro)

        if(stay){i--;}                                     //Decrementa contador, mantem loop infinito

        if (SerialBT.available()) {                        // Verifica se há dados disponíveis na conexão Bluetooth

          //tratamento comando recebido bt

          comando= SerialBT.readStringUntil('\n');

          comando.replace("\n", "");

          if(comando=="stop"){SerialBT.println("Forçada parada"); i=step;stay=0;}

        }

      }

      enable=0;

      //se o angulo foi colocado como negativo, é preciso corrigir o sentido de giro apos comando

      if(ang_neg){direcao=1; ang_neg=0;digitalWrite(pin_dir, direcao);}

    }

    SerialBT.println("\nEnvie um comando");

  }

}

````

#### Aplicativo 2: Controle remoto

![](content/images/direcao_app2.png)

![](content/images/direcao_app2_comandos.png)

Usaremos:
-       L (left)
-       R (right)
-       S (stop)
-       0 - 9 + q (speed)
-       D (close app)

##### Código acionamento (v3)

o valor pmin=166 foi obtido por estresse do motor em teste de bancada, maior velocidade para que não perca o passo na velocidade máxima (9)

````
#include "BluetoothSerial.h"

BluetoothSerial SerialBT;

//Definindo pinos

const int pin_ena = 15;                       //habilita o motor

const int pin_dir = 2;                        //determina a direção

const int pin_pul = 4;                        //executa um passo

//Variaveis para leitura mensagem bt

String comando;

//Variáveis para controle do motor

boolean direcao = HIGH;                               // low AH - high H

boolean enable = LOW;

int pmin=166;                                         //Para caso de pul_rev 400 (Experimental)

int velocidade=0;

volatile boolean pulso = LOW;                         //estado inicial do pulso (borda de decida)

volatile unsigned long periodo = pmin;                /* microsegundos (periodo minimo)

                                                      Frequencia do driver 200kHz (ou seja, 1 pulso em 1/200k=0.005 ms=5us).

                                                      O ideal seria 2.5 (positivo e negativo), mas vamos trabalhar com margem*/

void setup(){

  Serial.begin(9600);                        // Inicializa a porta serial para depuração

  pinMode(pin_ena, OUTPUT);

  pinMode(pin_dir, OUTPUT);

  pinMode(pin_pul, OUTPUT);

  digitalWrite(pin_ena, enable);

  digitalWrite(pin_dir, direcao);

  digitalWrite(pin_pul, pulso);

  SerialBT.begin("Controle remoto"); // Nome do dispositivo para comunicação BT

}

void loop(){

  if (SerialBT.available()) {                 // Verifica se há dados disponíveis na conexão Bluetooth

    //Tratar comando recebido

    comando= char(SerialBT.read());                 // Lê o comando enviado pelo dispositivo remoto

    switch(comando[0]){ 

      //L (left)

      case 'L':

        enable=1;direcao=0;

        //Serial.print(comando);Serial.println(" -> d0");

      break;

      //R (right)

      case 'R':

        enable=1;direcao=1;

        //Serial.print(comando);Serial.println(" -> d1");

      break;

      //S(stop)

      case 'S':

      case 'D':

        enable=0;

        //Serial.print(comando);Serial.println(" -> e0");

      break;

      //Speed

      case '0': case '1': case '2': case '3':

      case '4': case '5': case '6': case '7':

      case '8': case '9':

        velocidade=comando.toInt();

        periodo=pmin*(1+(10-velocidade)/2);

        //PA com razao sendo metade do periodo minimo

        //Serial.print(comando);Serial.printf(" p%i\n",periodo);

      break;

      case 'q':

        velocidade=10;

        periodo=pmin*(1+(10-velocidade)/2);

        //PA com razao sendo metade do periodo minimo

        //Serial.print(comando);Serial.printf(" p%i\n",periodo);

      break;

      default:

        Serial.println("\nERRO4");

      break;

    }

    digitalWrite(pin_ena, enable);

    digitalWrite(pin_dir, direcao);

    Serial.print(" p");Serial.print(periodo);

    Serial.print(" d");Serial.print(direcao);

    Serial.print(" e");Serial.println(enable);

    //Serial.printf("\nperiodo(us) = %i\nstep = %i\ndirecao = %i\nenable =  %i\n",periodo,step,direcao,enable);

    while(enable && comando[0]!='D' && comando[0]!='S'){

      pulso = !pulso;                                    //inverte o estado da variável

      digitalWrite(pin_pul, pulso);                      //atribui o novo estado à porta

      delayMicroseconds(((   periodo/2  )*2)/2);         //tempo em alta do pulso (divide por 2 e multiplica por 2 pra ter certeza q vai ser inteiro)

      pulso = !pulso;                                    //inverte o estado da variável

      digitalWrite(pin_pul, pulso);                      //atribui o novo estado à porta

      delayMicroseconds(((   periodo/2  )*2)/2);         //tempo em baixa do pulso (divide por 2 e multiplica por 2 pra ter certeza q vai ser inteiro)

      if (SerialBT.available()) {                        // Verifica se há dados disponíveis na conexão Bluetooth

        comando= char(SerialBT.read());

      }

    }

  }

}

````

# 2. Controle remoto

## 2.1. Possibilidades

1. Bluetooth: O Bluetooth é uma tecnologia sem fio de curto alcance que é amplamente utilizada em dispositivos móveis, como smartphones e fones de ouvido. É relativamente fácil de configurar e oferece uma boa taxa de transferência de dados para distâncias de até 10 metros em ambientes internos.
2. WiFi: O WiFi é um protocolo de comunicação sem fio de médio alcance que é amplamente utilizado em redes domésticas e empresariais. É mais complexo de configurar do que o Bluetooth, mas oferece taxas de transferência de dados mais altas e uma cobertura de alcance mais ampla para distâncias de até cerca de 50 metros em ambientes internos.
3. Zigbee: Zigbee é um protocolo de comunicação sem fio de curto alcance que é frequentemente usado em sistemas de automação residencial e industrial. É especialmente adequado para aplicações que exigem baixo consumo de energia e comunicação confiável para distâncias de até cerca de 10 metros.
4. LoRa: LoRa é um protocolo de comunicação de longo alcance que é adequado para aplicações que exigem cobertura em grandes áreas. Embora a distância máxima de comunicação possa ser superior a vários quilômetros em condições ideais, a taxa de transferência de dados é relativamente baixa. Portanto, é mais adequado para aplicações que exigem comunicação de baixa taxa de dados, como monitoramento de sensores remotos.

### 2.1.1. Phantom 4

![](content/images/direcao_phantom4.png)

O controle remoto do Phantom 4 utiliza uma tecnologia de comunicação proprietária da DJI, chamada de Lightbridge, que não é diretamente compatível com microcontroladores convencionais, como o Arduino

### 2.1.2. Arduino + módulo WiFi

![](content/images/Arduino_WiFi.png)

![](content/images/Arduino_WiFi2.png)

### 2.1.3. ESP para Raspberry (SPI)

Uma opção seria a leitura do controle remoto  feita pela raspberry (controlador) tal que esta envie os sinais para esp (periférico) que controlará o driver

#### Estudando Comunicação SPI

Na comunicação serial síncrona introduzimos o conceito de mestre - escravo (controlador - periférico), com apenas um controlador. Ou seja, um mesmo sensor (periférico) não pode estar em duas redes distintas, cada uma com um mestre diferente.

Além disso, os barramentos são unidirecionais, ou seja, os sinais são definidos somente como enviado ou somente como recebido. Mas é possível envio e recebimento de sinal de forma simultânea (por barramentos diferentes)

![](content/images/direcao_SPI.png)

![](content/images/direcao_SPI2.png)

Possível atingir velocidades maiores de comunicação porque há pouca deformação do sinal utilizando dois canais separados

##### Exemplo ligação

![](content/images/direcao_SPI3.png)

No SS, o dispositivo é selecionado quando este pino se encontra em nível baixo
MISO: serial data out (SDO)
MOSI: serial data in (SDI)

![](content/images/direcao_SPI4.png)

![](content/images/direcao_SPI5.png)

#### ESP32 (portas spi)

![](content/images/direcao_SPI6.png)

![](content/images/direcao_SPI7.png)

SP0 and SP1 are used internally to communicate with the built-in flash memory, and you should not use them for other tasks.

### !2.1.4. ESP para Raspberry (CAN)

#### Protocolo CAN

##### Características gerais           

![](content/images/direcao_CAN.png)

Protocolo CAN (Controller Area Network) é um protocolo de comunicação serial simultânea:
-       transmite dados a uma taxa de até 1 Mbps em barramentos de até 40 metros;
-       síncrono: sincronização no início de cada mensagem enviada ao barramento;
-       multicast: todas as mensagens são recebidas por todos os módulos da rede;
-       multi-mestre: todos módulos podem ser mestre ou escravo em determinado momento;
-       Suporta até 120 nós por barramentos;
-       CSMA/CD with NDA: caso duas mensagens sejam enviadas ao barramento ao mesmo instante, o módulo de menor prioridade cessará sua transmissão e o de maior continuará enviando sua mensagem, sem ter que reiniciá-la;
-       NRZ (Non Return to Zero): cada bit transmitido representa efetivamente um dado;
-       Protocolo regulado: ISO 11898
-       A velocidade de transmissão dos dados é proporcional ao comprimento do barramento;

![](content/images/direcao_CAN2.png)

O protocolo foi desenvolvido pela BOSCH

##### Configuração dos fios

Há três formas de construir o barramento (bus):
-       2 fios (dados: CAN H - high e CAN L - low),
-       4 fios (dado + VCC e GND, para alimentação dos blocos subsequentes)
-       1 fio (Somente 1 fio de dado: CAN).

O barramento é classificado como Par Trançado Diferencial (para 2 e 4 fios): os dados são interpretados pela análise da diferença de potencial entre os fios CAN H e CAN L, por isso não necessita blindagem (pois, caso haja efeito de campo externo, afetará ambos os sinais)

Para velocidades de comunicação acima de 250 kbit/s, é recomendado cabo em par trançado para a minimização de ruídos. É recomendado um resistor externo de 120 Ω/0,25 W para a rede CAN. A secção transversal de cada um dos fios deve ser de no mínimo 0,35mm²

##### Formato dos dados

Os dados são representados por bits recessivos e dominantes. O bit zero é dominante.  Após enviar um bit, cada módulo analisa o barramento e verifica se outro módulo na rede o sobrescreveu.

![](content/images/direcao_CAN3.png)

##### Formatos de mensagem:

-       CAN 2.0A (padrão): Mensagens com identificador de 11 bits, até 2048 mensagens

![](content/images/direcao_CAN4.png)

![](content/images/direcao_CAN5.png)

-       CAN 2.0B (estendido): identificador de 29 bits, até  5,3*10⁶ de mensagens, entretanto há aumento no tempo de transmissão de cada mensagem

![](content/images/direcao_CAN6.png)

![](content/images/direcao_CAN7.png)

##### Tipos de mensagens:

-       Frame de Dados: Contém os dados do emissor para o(s) receptor(es);

![](content/images/direcao_CAN8.png)

-       Frame Remota: É uma solicitação de dados partindo de um dos nós;

![](content/images/direcao_CAN9.png)

-       Frame de Erro: É um frame enviado por qualquer um dos nós ao identificar um erro no barramento e pode ser detectado por todos os nós;

![](content/images/direcao_CAN10.png)
-       Frame de sobrecarga: Serve para retardar o tráfego no barramento devido à sobrecarga de dados ou atraso em um ou mais nós.

![](content/images/direcao_CAN11.png)

##### Transceiver (MCP2515 -  Controlador CAN Stand-Alone com Interface SPI)

![](content/images/direcao_MCP2515.png)

Por que usar MCP2515 se a ESP32 ja tem uma porta CAN interna? Existem aplicações que requerem mais de uma porta CAN, e o único recurso do ESP32 aplicável para isso é a interface SPI. Na verdade, hipoteticamente, você pode adicionar até seis portas CAN controladas por SPI ao ESP32

![](content/images/direcao_MCP2515_pinout.png)

# 3. Encoder

![](content/images/direcao_encoder.png)

## 3.1. Possibilidades

### 3.1.1. Encoder externo

(Pegar os dados diretamente do motor)

teria que fazer uma redução com faixas escuras delimitadas para leitura por esse sensor. Ele envia luz e atua conforme a reflexão dela dada a sua reflexão.

![](content/images/direcao_encoder2.png)

Funciona como um amplificador. Mudando as resistências, altera a faixa que ele considera alta(3,3V) e baixa(0V). Assim, alteramos a sensibilidade do sensor (luz)

![](content/images/direcao_encoder3.png)

### 3.1.2. Software HSS86

(Pegar dos dados a partir do driver HSS86)

![](content/images/direcao_soft_HSS86.png)

![](content/images/direcao_soft_HSS86_2.png)

![](content/images/direcao_soft_HSS86_3.png)

![](content/images/direcao_soft_HSS86_4.png)

### 3.1.3. Encoder do próprio motor

#### Leitura encoder

Testes para p=1000us, pelo osciloscópio temos que o período do motor equivale a 400us para essa aplicação.

![](content/images/direcao_teste_encoder.jpg)
Sinais ea+ e eb+ são defasados em 90 (sentido horário d1)

![](content/images/direcao_teste_encoder2.jpg)
Sinais ea+ e eb+ são defasados em 90 (sentido anti horário d0)

![](content/images/direcao_teste_encoder3.jpg)
Sinais ea+ e ea- são opostos

#### ESP32 (AttachInterrupt)

![](content/images/direcao_pinout_attach.png)

função _attachInterrupt_, cujo protótipo é _attachInterrupt(GPIOPin, ISR, Mode)_. Onde:

●     **GPIOPin:** número do GPIO que quer monitorar utilizando interrupção externa;

●     **ISR:** nome da função a ser chamada na hora de tratar a interrupção;

●     **Mode:** modo da interrupção (LOW, HIGH, RISING, FALLING ou CHANGE).

No ESP32, há os seguintes modos de interrupção suportados para os GPIOs:

●     **FALLING:** um modo que faz ser gerada uma interrupção quando um GPIO vai do nível alto (3V3) para nível baixo (0V). Ou seja, interrupção gerada na transição de nível alto para baixo;

●     **RISING:** um modo que faz ser gerada uma interrupção quando um GPIO vai do nível baixo (0V) para nível alto (3V3). Ou seja, interrupção gerada na transição de nível baixo para alto;

●     **LOW:** um modo que faz ser gerada uma interrupção gerada quando o GPIO está em nível baixo;

●     **HIGH:** um modo que faz ser gerada uma interrupção gerada quando o GPIO está em nível alto;

●     **CHANGE:** um modo que faz ser gerada uma interrupção quando há qualquer transição de nível no GPIO. Ou seja, tanto de nível baixo para alto quanto de nível alto para baixo.

#### *Código encoder (falta testar)
````
const int pinA = 34;

const int pinB = 35;

const int debounceDelay = 50;

volatile int stateA, stateB, prevStateA, prevStateB;

volatile int counter = 0;

unsigned long lastDebounceTime = 0;

void IRAM_ATTR isrA() {

  stateA = digitalRead(pinA);

  stateB = digitalRead(pinB);

  if (millis() - lastDebounceTime > debounceDelay) {

    if (prevStateA == LOW && stateA == HIGH) {

      if (stateB == LOW) {

        counter++;

      } else {

        counter--;

      }

    } else if (prevStateA == HIGH && stateA == LOW) {

      if (stateB == LOW) {

        counter--;

      } else {

        counter++;

      }

    }

    prevStateA = stateA;

    prevStateB = stateB;

    lastDebounceTime = millis();

  }

}

void IRAM_ATTR isrB() {

  stateA = digitalRead(pinA);

  stateB = digitalRead(pinB);

  if (millis() - lastDebounceTime > debounceDelay) {

    if (prevStateB == LOW && stateB == HIGH) {

      if (stateA == LOW) {

        counter--;

      } else {

        counter++;

      }

    } else if (prevStateB == HIGH && stateB == LOW) {

      if (stateA == LOW) {

        counter++;

      } else {

        counter--;

      }

    }

    prevStateA = stateA;

    prevStateB = stateB;

    lastDebounceTime = millis();

  }

}

void setup() {

  pinMode(pinA, INPUT);

  pinMode(pinB, INPUT);

  attachInterrupt(digitalPinToInterrupt(pinA), isrA, CHANGE);

  attachInterrupt(digitalPinToInterrupt(pinB), isrB, CHANGE);

  Serial.begin(9600);

}

void loop() {

  Serial.println(counter);

  delay(100);

}

## 3.2. Dual core ESP32

Para leitura do encoder e controle do motor simultâneo serão utilizados os 2 processadores da esp

### *Código Multitask (falta testar)

#include <BluetoothSerial.h>

BluetoothSerial SerialBT;

TaskHandle_t TaskBluetooth;

TaskHandle_t TaskSerial;

///////////////////////////////////Inicialização tarefa 1: Encoder ////////////////////////////////////////////

//Definindo pinos

const int pin_ena = 15;                        //habilita o motor

const int pin_dir = 5;                        //determina a direção

const int pin_pul = 4;                        //executa um passo

//Variaveis para leitura mensagem bt

String comando;

String letra;

//Variáveis para controle do motor

boolean direcao = HIGH;                       // low AH - high H

boolean enable = LOW;

boolean pulso = LOW;                          //estado inicial do pulso (borda de decida)

int step = 4000;                              //Conta x pulsos

int periodo = 6;                              /* microsegundos (periodo minimo)

                                              Frequencia do driver 200kHz (ou seja, 1 pulso em 1/200k=0.005 ms=5us).

                                              O ideal seria 2.5 (positivo e negativo), mas vamos trabalhar com margem*/

//Variáveis para calculo/conversão

int tempo;

int angulo;

int pul_rev;

//Flags intermediarias

int ang_neg=0;                                 //flag sinal angulo

int flag=0;                                    //flag valor pul_rev recebido

int stay=0;                                    //flag comando permanecer ligado

void setup() {

  Serial.begin(9600);

  pinMode(pin_ena, OUTPUT);

  pinMode(pin_dir, OUTPUT);

  pinMode(pin_pul, OUTPUT);

  digitalWrite(pin_ena, enable);

  digitalWrite(pin_dir, direcao);

  digitalWrite(pin_pul, pulso);

  SerialBT.begin("Controle remoto"); // Nome do dispositivo para comunicação BT

  xTaskCreatePinnedToCore(

    TaskBluetoothCode,

    "Bluetooth",

    100000,

    NULL,

    1,

    &TaskBluetooth,

    0

  );

  xTaskCreatePinnedToCore(

    TaskSerialCode,

    "Serial",

    10000,

    NULL,

    1,

    &TaskSerial,

    1

  );

}

void TaskBluetoothCode(void *pvParameters) {

  while (1) {

    while(!flag){

      SerialBT.println("Informe valor pul_rev definido nas chaves do HSS86");

      delay(5000);

        if (SerialBT.available()) { //Verifica se há dados disponíveis na conexão Bluetooth

          comando=SerialBT.readStringUntil('\n');

          pul_rev=comando.toInt();

          switch(pul_rev){

            case 400:   case 800:   case 1000:  case 1600:

            case 2000:  case 3200:  case 4000:  case 5000:

            case 6400:  case 8000:  case 10000: case 12800:

            case 20000: case 25600: case 40000: case 51200:

                SerialBT.print("Pul_rev = ");  SerialBT.println(pul_rev);

                SerialBT.println("Envie um comando");

                flag=1;

                break;

            default:

              SerialBT.println("ERRO1. Obs: default=400");

              break;

          }

        }

    }

    if (SerialBT.available()) {

      comando= SerialBT.readStringUntil('\n');  // Lê o comando enviado pelo dispositivo remoto

      comando.replace("\n", "");                // Remove o caractere '\n' da string

      letra=comando[0];

      comando.replace(letra, "");

      switch(letra[0]){ 

        //Direção

        case 'D':

        case 'd':

          if(comando[0]=='0'){SerialBT.println("mudei direção pra 0");direcao=0;}

          else if(comando[0]=='1'){SerialBT.println("mudei direção pra 1"); direcao=1;}   

          else{SerialBT.println("ERRO2");}

          SerialBT.print("dir = "); SerialBT.println(direcao);

          digitalWrite(pin_dir, direcao);

        break;

        //Enable

        case 'E':

        case 'e' :

          if(comando[0]=='0'){enable=0;}

          else if(comando[0]=='1'){enable=1;}   

          else{SerialBT.println("ERRO3");}

          digitalWrite(pin_ena, enable);

          SerialBT.print("ena = "); SerialBT.println(enable);

        break;

        //Periodo em us

        case 'P':

        case 'p':

          periodo=comando.toInt();  //forçar que o numero seja par

          SerialBT.print("periodo (us) = "); SerialBT.println(periodo);

        break;

        //Numero de passos (Steps)

        case 'S':

        case 's':

          if(comando[0]=='.'){SerialBT.println("Ficará ligado até comando stop"); step=1; stay=1;}

          else{

            step=comando.toInt();

            SerialBT.print("step = ");   SerialBT.println(step);

          }

        break;

        //Angulo de rotação em graus

        case 'A':

        case 'a':

          SerialBT.print("angulo = ");

          if(comando[0]=='-'){ang_neg=1; direcao=0; comando.replace("-", "");SerialBT.print("-");}

          else{ang_neg=0; direcao=1; SerialBT.print("+");}     

          digitalWrite(pin_dir, direcao);

          angulo=comando.toInt(); 

          step=angulo*(pul_rev)/360;

          SerialBT.println(angulo);

        break;

        default:

          SerialBT.println("ERRO4");

        break;

      }

      if(periodo<5){periodo=6;SerialBT.println("ERRO5"); SerialBT.println("OBS: Periodo mínimo = 5us (fmax=200kHz). Periodo setado para 6us");}

      else{SerialBT.println("frequencia dentro da permitida>5us");}

      SerialBT.print("\nperiodo (us)= ");  SerialBT.println(periodo);

      SerialBT.print("step = ");  SerialBT.println(step);

      SerialBT.print("direcao = ");  SerialBT.println(direcao);

      SerialBT.print("enable = ");  SerialBT.println(enable);

      if(enable){

        SerialBT.println("Iniciado");

        for(int i=0; i<step; i++){

          pulso = !pulso;                                    //inverte o estado da variável

          digitalWrite(pin_pul, pulso);                      //atribui o novo estado à porta

          delayMicroseconds(((   periodo/2  )*2)/2);         //tempo em alta do pulso (divide por 2 e multiplica por 2 pra ter certeza q vai ser inteiro)

          pulso = !pulso;                                    //inverte o estado da variável

          digitalWrite(pin_pul, pulso);                      //atribui o novo estado à porta

          delayMicroseconds(((   periodo/2  )*2)/2);         //tempo em baixa do pulso (divide por 2 e multiplica por 2 pra ter certeza q vai ser inteiro)

          if(stay){i--;}

          if (SerialBT.available()) {                        // Verifica se há dados disponíveis na conexão Bluetooth

            comando= SerialBT.readStringUntil('\n');         // Lê o comando enviado pelo dispositivo remoto

            comando.replace("\n", "");                       // Remove o caractere '\n' da string

            if(comando=="stop"){SerialBT.println("Forçada parada"); i=step;stay=0;}

          }

        }

        enable=0;

        //se o angulo foi colocado como negativo, é preciso corrigir o sentido de giro apos comando

        if(ang_neg){direcao=1; ang_neg=0;digitalWrite(pin_dir, direcao);SerialBT.println("Angulo negativo, direçao resetada");}

        SerialBT.println("Comando recebido: " + comando);

        SerialBT.println("\nEnvie um comando");

      }

    }

    // Adicione aqui qualquer outra lógica relacionada à tarefa Bluetooth

    vTaskDelay(100 / portTICK_PERIOD_MS);

  }

}

void TaskSerialCode(void *pvParameters) {

  while (1) {

    Serial.println("oi mundo");

    // Adicione aqui qualquer outra lógica relacionada à tarefa Serial

    vTaskDelay(1000 / portTICK_PERIOD_MS);

  }

}

void loop() {}
````

# 4. Bibliografia

[1]  [Dados motor de passo NEMA 34](https://www.fernandok.com/)
[2]  [Sinalização erro LED HSS86]([HSS86 and 2HSS86H Closed Loop Drivers (cnczone.com)](https://www.cnczone.com/forums/servo-motors-drives/356990-software.html))
[3]  [Comunicação CAN na ESP32 ](https://www.fernandok.com/2018/07/protocolo-can-yes-we-can.html)
[4]  [Uso software HSS86](https://www.youtube.com/watch?v=2x_IKvZJIPQ)
[5]  [Projeto encoder com arduino](https://easytromlabs.com/arduino/arduino-lab-09-leitura-de-um-encoder-industrial-heidenhain-com-o-arduino/)
[6]  [Uso de interrupções na ESP32]([Uso de interrupções externas com ESP32 - MakerHero](https://www.makerhero.com/blog/uso-de-interrupcoes-externas-com-esp32/)
[7]  [Introdução geral sobre rede CAN](http://www.alexag.com.br/Artigos/SAE2002.pdf)
[8]  [Tipos de CAN (low speed, high speed and FD)](https://dewesoft.com/blog/what-is-can-bus)

<!--
Motor DC 4000 RPM redução 1:8 encoder 15 passos, plataforma: Arduino
![[esquema_direcao.jpg]]
-->