# Introdução

Inicialmente usávamos o RPI 4, mas mudamos para a OrangePi 5b com mais cores da CPU, GPU melhor, CAN nativo e um chip dedicado para IA. 

Como trabalhamos com um sistema "importante", que é um sistema automotivo, precisamos seguir padrões de segurança no hardware e no código, além disso, a nossa ECU(unidade de controle) não pode falhar ou se entreter em outras tarefas durante o loop de operação, é necessário que as tarefas imporantes sejam devidamente priorizadas e agendadas em relação às outras tarefas mais comuns.

Não é desejável que no momento do sistema reagir a uma situação de perigo ele esteja ocupado checando o clima para o widget do desktop, portanto, precisamos de um sistema em tempo real.

[fale sobre a filosofia RT]

# Patch do kernel

Antes de fazer isso espero que tenha instalado no eMMC, a memória embarcada que nos isenta de usar cartão SD, siga os passos no manual e volte aqui.

Para que o linux na OrangePi seja tempo real temos duas opções:
- baixar um patch sob medida
- compilar nosso próprio kernel

O fabricante não disponibilizou patch RT, então precisamos baixar o source do kernel e compilar de outra forma. Por sorte ao menos o fabricante disponibiliza o kernel com arquivo de configuração, com isso não é necessário se preocupar em configurar coisas demais, apenas o kernel RT.

Com uma pesquisa do google você pode encontrar o manual e seguir os passos da seção 6.1 antes de fazer `make install`, mas pode ir 100% por aqui.

### Kernel Source
Baixar source do kernel, execute:
```
git clone --depth=1 -b orange-pi-5.10-rk3588 https://github.com/orangepi-xunlong/linux-orangepi
```

### Configurações antes do PREEMPT_RT
Quando terminar de clonar
```
cd linux-orangepi
make rockchip_linux_defconfig
```
Com isso, já temos o que é necessário para que o kernel compile para a nossa placa com todos os drivers corretamente. Mas para compilarmos o kernel em tempo real, fazemos isso:
Edite esse arquivo, e adicione as proximas linhas no final dele
```
sudo nano kernel/Kconfig.preempt
```

```
config PREEMPT
       def_bool y

config ARCH_SUPPORTS_RT
       def_bool y
```

### Configurar compilação para PREEMPT_RT
Dê ctrl+o, enter e ctrl+x, ou seja, salvar no mesmo nome e sair do arquivo.

Agora rode o comando: `make menuconfig`

Faça o seguinte caminho: General setup -> Preemption Model, selecione com o espaço Fully preemptible kernel, enter. 

![](images/krt1.png)

![](images/krt2.png)

![](images/krt3.png)

Na tela anterior aperte save, dê ok e já pode sair.

### Compilando e aplicando o patch
Os próximos comandos são mais diretos e fáceis, execute um após o outro.
```
make -j10
sudo make modules_install
sudo make install
sudo make dtbs_install INSTALL_DTBS_PATH=/boot/dtb/
sudo reboot
```
Para verificar faça `uname -a` e espere ver algo do tipo: `Linux orangepi5b 5.10.110+ #1 SMP PREEMPT_RT Mon Aug 7 13:50:28 -03 2023 aarch64 aarch64 aarch64 GNU/Linux`, se tiver PREEMPT_RT foi sucesso.

Se você não instalou no eMMC anteriormente, siga os passos no manual e faça isso aqui tudo de novo.