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

