
---
title: "Controle via CANopen"
tags:
- elétrica
- sensoriamento
weight: -5
date: "2023-01-03"
---

### Links
- Para detalhes do uso do CAN no inversor → [Manual CANopen](https://static.weg.net/medias/downloadcenter/h95/haf/WEG-cfw11-canopen-manual-da-comunicacao-0899.5746-manual-portugues-br.pdf)

- Manual com informações principais → [CVW300 Manual do Usuário](https://static.weg.net/medias/downloadcenter/h72/h09/WEG-CVW300-G2-manual-do-usuario-10005423031-pt.pdf.pdf) (basicamente do 700 não se encontra, usamos o CVW300 mesmo)

# Introdução
CANopen é um protocolo de comunicação de rede amplamente utilizado na indústria, especialmente na automação industrial. Ele foi desenvolvido como uma forma padronizada de comunicação entre dispositivos eletrônicos em uma rede [CAN (Controller Area Network)](https://en.wikipedia.org/wiki/CAN_bus).

# Funcionamento
O funcionamento do CANopen é baseado fundamentalmente no protocolo CAN, mas inclui uma camada de abstração adicional que permite uma comunicação mais flexível e robusta entre os dispositivos industriais. Ele inclui um conjunto de mensagens padronizadas que podem ser usadas para enviar e receber dados, além de mecanismos de gerenciamento de rede e diagnóstico.

A implementação do CANopen envolve a conexão de dispositivos eletrônicos compatíveis através de uma rede CAN física, usando cabos e conectores padronizados. Cada dispositivo na rede é atribuído a um endereço único e pode ser configurado para enviar ou receber mensagens específicas. Um gerenciador de rede então pode ser usado para configurar e monitorar os dispositivos (como sensores e o próprio inversor de frequência) na rede, bem como para diagnosticar eventuais problemas.

# Parâmetros principais (684 e 685)
### 0684 - Palavra de controle CAN

![](notes/images/palavracontrolecan.png)

A ordem é esssa mesma, você vai fazer 7:0 e 8:15, ou seja, XXFF, tanto faz o do segundo byte. Precisa habilitar pra poder liberar o motor ao movimento, mas não sei se precisa pro JOG, no entanto, com os bits 0 e 1 você vai ter o motor girando. É interessante que o motor esteja com o remoto habilitado para que todas referêcias definidas nos próximos parâmetros estejam corretas e não haja ocorrência da rotação estar ao contrário, por exemplo. 

Segunda rampa define se vai o bit 0 vai habilitar a rampa 1 ou 2, a rampa 1 é definida pelos parametros 100 e 101, a segunda pelos 102 e 103.

Outro dia procurava como resetar falhas pelo CAN, pois bem, é por esse bit 7, setando ele para 1 a falha reseta.

### 0685 - Referência de velocidade CAN

O inversor somente usará se os parâmetros 221 e 222 estiverem definidos para receber referências via CAN. 

![](notes/images/refvelcan.png)

Para mais detalhes veja a página 19 do manual CANopen.

# Outros parâmetros setados anteriormente, reconfigure só se precisar

**0700 - Protocolo CAN automotivo** - deixar em 1 pra ativado, fizemos isso e salvamos definindo o parametro 303 para 1, isso serve para salvar qualquer mudança nos parametros.

**0701 - Endereço CAN** → não é relevante para nós, ele vai escutar qualquer mensagem, visto que usamos o protolo mais simples de CAN, definimos mensagens no configurador CAN do WEG WPS e assim interagimos com os parâmetros.

**0702 - Taxa do CAN** → definimos em 2 para 500kbps, não foi possível configurar acima disso quando usando o módulo MCP2515, mas podemos tentar aumentar usando o CAN nativo da OrangePi

**0703 - Reset de Bus Off** → definimos em 1 para automático, assim o próprio inversor se resove em questão de problemas no bus.

**0705 - Estado CAN (somente leitura)**

![](notes/images/paramestadocan.png)

**P0303 - Salvar Parâmetros →** 1 para salvar os parametros de agora

# Definindo mensagens

**RESUMINDO**: as mensagens principais são todas essas de Rx menos as 216 e 681 que nada têm a ver com o CAN, deixei e não tirei ainda. O resto menos 684 e 685 definem seleção de referência, 684 controla efetivamente ativação ou não do inversor, e 685 é o próprio RPM a ser atingido.

O software WEG WLP permite 10 mensagens para enviar e 10 para receber, definimos no campo COBID o valor que vai aparecer como ID do telegrama CAN, em WORD definimos que parâmetro será lido/escrito por esse COBID, exemplo

![](notes/images/configuradormensagenscan.png)

COBID 685 para enviar é o parametro 685 de velocidade, o telegrama com essa ID define a velocidade. 1685 envia o parametro 685, ou seja, o valor que definimos. Isso se dá do ponto de vista do inversor, Tx é transmitido por ele e Rx é recebido por ele. Rx é o que vamos escrever externamente. 

Lembrando que serão só 10 tx e 10 rx, se precisar mexer em mais coisa, ou usar todas as words da mensagem, coisa que eu ainda não tentei, parece que funciona, sabendo que cada word é 16 bits no máximo, dá pra controlar tudo. Mas enfim, ou use mais words se precisar MUITO ou configure manualmente no HMI.

# Mudar para referência CAN
Pra trocar referencia de tudo para remoto e usar CAN enviamos essas mensagens, já em hexadecimal. O exemplo se dá usando o comando cansend do pacote `can-utils` via interface can0, ou seja, de forma manual, pois usamos o node-red para isso com o botão setup.

```bash
cansend can0 220#01
cansend can0 222#06
cansend can0 226#00
cansend can0 227#03
cansend can0 228#04
cansend can0 684#00
```

OBS.: ainda fico indeciso se deixa ele todo local ou todo remoto antes de começar a execução do programa que vai configurar e controlar o inversor.

# Outros parâmetros úteis
205 → parametro para aparecer no display maior, atualmente valor de RPM lido pelo encoder, não é o setpoint

206 → escolhe o parametro pra poder aparecer no dispaly de cima, o que geralmente mostra o valor de RPM do CAN (parametro 685)

207 → Seleciona valor da barra gráfica, geralmente com a corrente do motor, vai de 0 a 100%, mas aparenta ser configurável e dependente do parâmetro de corrente nominal do moto.

209 → Escolhe a unidade do display maior, fica ao lado do display maior, atualmente RPM

O valor de display pode mudar para qual unidade é usada, inclusive, se usar km/h, isso pode ser dependente do diâmetro da roda, é preciso ler no manual que diâmetro exatamente ele tá falando.
