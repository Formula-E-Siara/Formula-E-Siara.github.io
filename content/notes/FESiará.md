---
title: "Página Inicial"
permalink: /start
---

# Então você quer saber mais sobre o Fórmula-E Siará?

Nossa equipe é formada por alunos de diferentes cursos de engenharia e áreas afins, que trabalham em conjunto para criar um carro inovador, eficiente e competitivo.

Neste site, você vai conhecer um pouco mais sobre o nosso projeto e as principais características do nosso carro. Vamos apresentar os sistemas que compõem o veículo e os desafios que enfrentamos para desenvolvê-los. Esperamos que você se inspire com a nossa história e se apaixone pelo Fórmula-E Siará!

# Chassi

O chassi é a estrutura que sustenta todos os componentes do veículo. Ele deve ser leve, resistente e rígido o suficiente para garantir a segurança dos ocupantes e o desempenho dinâmico do carro. O nosso chassi é feito de tubos de aço (SAE 1020) soldados com o processo MIG/MAG, realizado por alunos da UFPB. Ele possui uma célula de sobrevivência para o piloto, com cintos de segurança de cinco pontos e uma barra antivolta. O chassi também incorpora pontos de fixação para os sistemas de suspensão, freio, direção, propulsão e baterias.

# Suspensão

A suspensão é o sistema responsável por conectar as rodas ao chassi e absorver as irregularidades do solo. Ela deve proporcionar estabilidade, conforto e aderência ao veículo. A nossa suspensão é do tipo duplo A (double wishbone), com quatro braços triangulares por roda, que permitem ajustar a geometria da suspensão de acordo com as condições da pista. A suspensão também conta com amortecedores a gás e barras estabilizadoras, que controlam o movimento vertical e lateral do carro.

# Sistema de Freio

O sistema de freio é o sistema responsável por reduzir a velocidade ou parar o veículo. Ele deve ser confiável, eficaz e modulável. O nosso sistema de freio é composto por quatro discos ventilados de aço inoxidável, acionados por pinças de quatro pistões cada. O sistema também possui um cilindro mestre duplo com um regulador de pressão entre os circuitos dianteiro e traseiro, que permite ajustar o equilíbrio de frenagem entre as rodas. Além disso, o sistema conta com um dispositivo anti-bloqueio (ABS), que evita que as rodas travem em situações de emergência.

# Sistema de Propulsão (Powertrain)

O sistema de propulsão é o sistema responsável por gerar e transmitir a potência do motor para as rodas. Ele deve ser potente, eficiente e durável. O nosso sistema de propulsão é baseado em um motor elétrico síncrono de ímã permanente, que produz 80 kW (109 cv) de potência máxima e 220 Nm (22,4 kgf.m) de torque máximo. O sistema também possui uma transmissão por corrente e uma embreagem centrífuga, que acopla ou desacopla o motor das rodas trase

## Inversor de Frequência

O inversor de frequência é o dispositivo responsável por controlar a velocidade do motor elétrico, variando a frequência e a tensão que é fornecida a ele. Ele converte a corrente alternada fixa da rede elétrica em uma corrente alternada variável, usando uma técnica chamada modulação por largura de pulso (PWM). O inversor de frequência permite ajustar a velocidade do motor de acordo com a demanda da operação, otimizando o consumo de energia e o desempenho do veículo. O nosso inversor de frequência é um modelo compacto e robusto, que possui proteções contra sobrecarga, curto-circuito e sobretensão. Ele também possui uma interface de comunicação com o sistema de eletrônica embarcada, que permite monitorar e controlar os parâmetros do motor.

## Eletrônica

A eletrônica é o conjunto de circuitos e componentes que gerenciam e controlam os diversos sistemas do veículo. Ela é responsável por coletar, processar e transmitir dados e sinais entre os sensores, atuadores, dispositivos de entrada e saída e o sistema de telemetria. A nossa eletrônica é dividida em módulos que se comunicam por meio de um barramento CAN (Controller Area Network), que garante uma alta velocidade e confiabilidade na troca de informações. Alguns dos módulos que compõem a nossa eletrônica são:

### Direção

O módulo de direção é responsável por controlar o sistema de direção elétrica assistida (EPS), que auxilia o piloto na condução do veículo. O EPS consiste em um motor elétrico acoplado à coluna de direção, que aplica um torque proporcional à força exercida pelo piloto no volante. O módulo de direção recebe o sinal do sensor de torque no volante e envia um comando para o motor do EPS, ajustando o nível de assistência conforme a velocidade do veículo. O módulo também monitora a temperatura e a corrente do motor do EPS, além de enviar dados para o sistema de telemetria.

### Baterias

O módulo de baterias é responsável por monitorar e proteger o pacote de baterias que alimenta o motor elétrico. O pacote é composto por 120 células de íons de lítio, conectadas em série e paralelo, que fornecem uma tensão nominal de 400 V e uma capacidade de 15 Ah[3](https://blog.kalatec.com.br/inversor-de-frequencia/). O módulo de baterias mede a tensão, a corrente e a temperatura de cada célula, além do estado de carga (SOC) e do estado de saúde (SOH) do pacote. O módulo também controla os relés e os fusíveis que isolam o pacote da rede elétrica do veículo em caso de falha ou emergência. O módulo ainda envia dados para o sistema de telemetria e para o inversor de frequência, que usa essas informações para otimizar o controle do motor.

## Telemetria

A telemetria é o sistema responsável por transmitir e receber dados entre o veículo e uma estação base. Ela permite acompanhar em tempo real o funcionamento e o desempenho do veículo durante os testes e as provas da competição. A nossa telemetria usa um transceptor sem fio que opera na faixa de 2,4 GHz, com uma taxa de transmissão de 250 kbps. O transceptor se comunica com um computador na estação base, que exibe os dados em uma interface gráfica desenvolvida pela equipe. A telemetria também permite enviar comandos para o veículo, como ligar ou desligar sistemas ou alterar parâmetros de controle.

Considerando essenciais os sentores: IMU, GPS, de velocidade das rodas, LIDAR e câmeras. Freio, propulsão, suspensão → serão analisados e incluídos futuramente. Então só vamos trabalhar com dados dos primeiros tanto para transmitir tanto quanto para dar entrada no controle. 

Para o sensor de RPM a melhor solução é fazer como em carros comerciais, usando o sensor ABS. Que se trata de um sensor indutivo acoplado mirando os dentes da engrenagem do eixo da roda, possibilitando haver uma boa resolução em velocidades altas ou baixas, não pode ser confundido com a saída do eixo do motor, em que pode haver uma redução dele para as rodas. Este sensor mede o RPM real das rodas, não a velocidade do motor.

Sobre a Inter comunicação(interna), todos subsistemas se comunicarão com o protocolo CAN, assim como é feito em modelos comerciais e carros de maior porte. Será um sistema híbrido com vários controladores e sensores, independentes, mas trabalhando em conjunto com o computador principal, o qual processa vídeo e envia dados para a telemetria.

# [[Escolha de sensores]]

# [Simulador](./telemetria/simulador.md)

# [[Controle manual à distância]]
