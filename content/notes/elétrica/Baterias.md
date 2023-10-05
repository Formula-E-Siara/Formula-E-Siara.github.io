---
title: "Baterias"
tags:
- elétrica
weight: -5
date: "2022-09-06"
---

# Objetivos e Desafios
Obter dados do BMS via CAN ou RS485, tensão, corrente e temperatura.
~~A Bateria precisa ter uma capacidade de carga razoável~~
~~A Bateria precisa ser carregada com desgaste mínimo~~
~~Precisamos de [[notes/elétrica/Battery Managment System|monitoriamento]] constante (pra reduzir o desgaste e manter segurança química da bateria)~~
# Como Estamos?
Utilizávamos 4 baterias de chumbo-ácido de 12V em série, com capacidade individual de 115Ah. Mas, atualmente já fizemos a troca por bateria de lítio, 60v 91Ah com BMS integrado.
![](notes/images/BMS/BMS_frente.jpg)
![](notes/images/BMS/carrregador_BMS.jpg)
# Futuro
~~Substituição por baterias de [Íon de Lítio](notes/elétrica/Estudo%20de%20Baterias%20Substitutivas.md)~~ Efetuado em set/23.

# Configuração do BMS
Para configuração do BMS temos o aplicativo via bluetooth e o programa do fabricante. Os arquivos são disponibilizados na pasta *arquivos_para_download* nesse repositório.
O aplicativo é intuitivo, na primeira tela é possível ler dados de corrente, tensão das células e MOSFET de saída. No botão superior esquerdo temos algumas opções, onde a mais
importante é o menu de definir os parâmetros. Ao conectar via bluetooth o aplicativo pode exigir uma senha, que é 123456.

⚠️**ATENÇÃO**⚠️
Essa é uma das coisas que você deve fazer com alguém do lado para checar e seguir o procedimento. Não mude os parâmetros do BMS antes salvar os parâmetros atuais. Pois
será necessário contatar o fabricante e corrigir. Não mexer com parâmetros de tensão, capacidade, número de baterias ou proteção e proteção não deve ser muito mais alta do que está 
determinado, pois alta corrente pode causar danos à bateria.

