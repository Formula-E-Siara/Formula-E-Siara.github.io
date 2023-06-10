---
title: "Simulador EUFS"
tags:
- elétrica
- telemetria
weight: -5
date: "2023-04-26"
---

Decidimos por usar um simulador quase de acordo com as necessidades do projeto. Usamos uma versão modificada do simulador da equipe de Formula da Universidade de Edimburgo. Em caso de dúvidas, sinta-se convidado(a) a enviar criar uma issue no github.

## Como instalar o simulador
Infelizmente somente funciona em linux, algumas dependências do simulador são do linux apesar da possibilidade de usar o ROS no Windows. No entanto, é possível usar o WSL ou o VirtualBox.

OBS.: o site do EUFS lista Ubuntu 22 e ROS Galactic, mas aqui usamos outras versões. 

### Requerimentos
- Ubuntu 22.04
- git
- lsb_release
- ROS 2 Humble

### Primeiros passos
Primeiramente, execute
```
sudo add-apt-repository main
sudo add-apt-repository universe
sudo apt install lsb-core lsb-release -y
```
Serve para que o script do ROS detecte a versão correta do Ubuntu, isso realmente deu uma vez erro e consumiu muito tempo. 

Em seguida vá na página do <a href="https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html">ROS Humble</a>, seguindo os passos tudo deve funcionar normalmente. Quando for fazer o `sudo apt install ros-humble-desktop`, não faça, faça isso: `sudo apt install ros-humble-desktop`, para instalar todos os pacotes. Ainda vai faltar, então faça isso:
```
sudo apt install ros-humble-desktop-full
```
e depois instale esses pacotes:
```
sudo apt install ros-humble-gazebo-ros ros-humble-ackermann-msgs
```

Agora para outras dependências do simulador, faça
```
pip3 install colcon-common-extensions -U
# se você não tiver python:
sudo apt install python3 python3-pip
```

Faça:
(Obs.: não coloque o simulador numa pasta cujo nome tenha acentos ou espaços como, ex.: Zé Maria, isso impede que o seu terminal encontre a pasta de onde instalar os pacotes)
```
cd ~/

# efetivamente fazemos o download dele
git clone https://github.com/Formula-E-Siara/simulador

# entre no diretório
cd simulador


echo 'export EUFS_MASTER=$(pwd)' >> ~/.bashrc

source ~/.bashrc
```

Vamos iniciar o workspace do ros, se você não tiver feito
```
source /opt/ros/humble/setup.bash
```

Esses comandos iniciam o gerenciador de pacote e instalam os pacotes do simulador que baixamos no `git clone`
```
sudo apt-get install python3-rosdep
sudo rosdep init
rosdep update
rosdep install --from-paths $EUFS_MASTER --ignore-src -r -y
```

Volte para a pasta do simulador e faça:
```
# entrar na pasta
cd $EUFS_MASTER

# dentro da pasta
colcon build
```

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

# para iniciar o simulador
ros2 launch eufs_launcher eufs_launcher.launch.py
```
