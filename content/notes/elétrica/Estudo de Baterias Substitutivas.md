---
title: "Estudo de Baterias Substitutivas"
tags:
- elétrica
weight: -5
date: "2022-09-08"
---

Ressalvas iniciais:

As baterias consideradas inicialmente foram baterias lítio cilíndrica, em especial pelo seu valor e pela menor necessidade de cuidados especiais que são necessários nas células prismáticas (Maior cuidado com a temperatura e pressão do ambiente).

Pressupondo um nível médio de tensão por célula de 3.6V. Com tensão total necessária de 72V e Energia Total aproximada de [6.5kWh](https://webthesis.biblio.polito.it/15660/), obtemos as seguintes configurações:
 

---:|Sony VTC6 :| LG HG2 :| Samsung 30Q :| Samsung 30T :| Samsung 40T :| Melasta LPA542126
---|-------------|-----|-----------|-------------|-------------|--------------------
Dimensions|18650 |18650 | 18650 | 21700 | 21700 | Primastic
Nominal capacity [mAh] | 3120 | 3000 | 3040 | 3000 | 4000 | 6000
Nominal voltage [V] | 3,6 | 3,6 | 3,6 | 3,6 | 3,6 | 3,7
Energy density [Wh/g] | 0,240 | 0,230 | 0,240 | 0,158 | 0,216 | 0,175
Internal impedance [Ω] | 0,13 | 0,20 | 0,13 | 0,13 | 0,12 | 
Maximum continuous discharge current [A] | 20 | 20 | 15 | 35 | 35 | 56
Energy/pack of 20 [Wh] | 224.64 | 216 | 218.88 | 216 | 288 | 444
Number of Packs to supply 6.5kWh | 28.9 | 30.1 | 29.7 | 30.1 | 22.6 | 14.6
Total number of Cells | 600 | 640 | 600 | 640 | 480 | 320
Total weight (Only Cells) [kg] | 25.0 | 26.1 | 25.0 | 38.0 | 27.8 | 34.3

Tendo uma tensão total de 72V, devemos "serializar" 20 células. (72V\\3.6V)
Além disso, temos dois limitantes para o número de packs: a Energia Total e a Corrente de descarga. Nosso contingente de Energia atual totaliza 5.5kWh.

Para calcular a Corrente que será puxada da nossa bateria, basta especificarmos a potência da nossa carga. Pelas especificações, obtemos: 7.5kW do motor, 0.6kW em perdas do Inversor e 0.4kW de componentes eletrônicos, obtemos um total de 8.5kW.<br/>
Portanto, podemos esperar uma corrente de 120A (8.5kW\\72V). Criando um limitante mínimo para cada possível célula:

---|Sony VTC6 | LG HG2 | Samsung 30Q | Samsung 30T | Samsung 40T | Melasta LPA542126
---|-------------|-----|-----------|-------------|-------------|--------------------
Maximum continuous discharge current [A] | 20 | 20 | 15 | 35 | 35 | 56
Minimum number of parallel 20 cells' packs to support the expected system's current| 6.0 | 6.0 | 8.0 | 3.4 | 3.4 | 2.1
