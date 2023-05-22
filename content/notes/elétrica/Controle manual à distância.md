Para controle do carro no simulador ou mesmo fisicamente, existe a opção de usar um controle especializado, como os drones possuem, entretando, havendo boa infraestrutura de telemetria podemos usar alguns controles mais simples como teclado ou mesmo um gamepad, havendo confiabilidade e mecanismos de segurança. Nesse sentido temos o módulo `ds4input` que recebe entrada de um controle de PS4 e transmite via tópicos do ROS para controle do carro. 

No momento faça: `ros2 run ds4input main`

O código escreverá a mensagem AckermannSteeringStamped no tópico `/cmd`
Após abrir o simulador apenas colocar em modo manual, e a entrada via controle funciona, não mexer com comandos de velocidade do launcher usando o controle, até o momento a integração não é completa.