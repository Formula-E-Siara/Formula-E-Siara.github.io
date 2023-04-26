---
title: "Simulador"
tags:
- elétrica
- telemetria
weight: -5
date: "2023-04-26"
---

<!---
OBS: meu repoositório no github funciona, mas quero que seja um fork de VERDADE, porque quando baixei, editei e tentei upar, tive problema com os submódulos, se fosse um fork seria tranquilo, mas pelo gitlab não sei como fazer, então por enquanto vai assim mesmo.
--->

Decidimos por usar um simulador quase de acordo com as necessidades do projeto. Usamos uma versão modificada do simulador da equipe de Formula da Universidade de Edimburgo. 

<a href="https://github.com/gustavosousa2208/eufs_modified">SIMULADOR</a>

## Como instalar o simulador
Infelizmente somente funciona em linux, algumas dependências do simulador são do linux apesar da possibilidade de usar o ROS no Windows. No entanto, é possível usar o WSL ou o VirtualBox.

### Requerimentos
- Ubuntu 20.04
- git
- lsb_release
- ROS 2 Humble

### Primeiros passos
Primeiramente, execute
```
sudo apt install lsb-core lsb-release -y
```
Serve para que o script do ROS detecte a versão correta do Ubuntu, isso realmente aconteceu e consumiu muito tempo. Após isso vamos compilar o ROS da fonte, demora bastante mas, será uma só vez e evita problemas com pacotes no futuro.

Em seguida vá na página do <a href="[Ubuntu (source) — ROS 2 Documentation: Humble documentation](https://docs.ros.org/en/humble/Installation/Alternatives/Ubuntu-Development-Setup.html)">ROS Humble</a>, seguindo os passos tudo deve funcionar normalmente. 

Na hora de instalar o simulador siga o tutorial do EUFS normalmente, no entanto, ao ao chegar na parte de rosdep install, faça source do script do ROS, exemplo, baixei e compilei, abro a pasta que salvei e faço:
```
# se você usar zsh use .zsh ao invés de .bash
source ./install/setup.bash
```
Se faltarem módulos na hora, pegue o nome deles e substitua underline por "-", e faça conforme o exemplo: 
```
# exemplo ackermann_msgs
sudo apt install ros-humble-ackermann-msgs
```
Depois colcon build e pronto. Daqui sempre que for usar o simulador faça, entre na pasta do simulador e faça
```
# caso não estiver na pasta, faça:
cd ~/eufs_modified && . install/setup.bash
# caso estiver na pasta faça
. install/setup.bash
# se não der certo substitua o . por source
```
