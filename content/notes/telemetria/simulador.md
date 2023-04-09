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
- ROS 2 Galactic

### Primeiros passos
Primeiramente, execute
```
sudo apt install lsb-core lsb-release -y
```
Serve para que o script do ROS detecte a versão correta do Ubuntu, isso realmente aconteceu e consumiu muito tempo. Após isso vamos compilar o ROS da fonte, demora bastante mas, será uma só vez e evita problemas com pacotes no futuro.

Em seguida vá na página do <a href="https://docs.ros.org/en/galactic/Installation/Alternatives/Ubuntu-Development-Setup.html">ROS Galactic</a>, seguindo os passos tudo deve funcionar normalmente. 