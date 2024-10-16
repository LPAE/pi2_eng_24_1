O projeto consiste em um conjunto de sensores e atuadores que monitoram e controlam uma esteira, onde passam objetos de diferentes cores e tamanhos. Esses sensores são responsáveis por detectar as características dos objetos, enquanto os atuadores assumem o controle e manipulação ou fornecem informações com base nos dados recebidos dos sensores.

#### Os seguintes dispositivos foram pensados para essa esteira:

##### Sensores:

*   Sensor de cor: detecta a cor dos objetos e envia essa informação ao display.
*   Sensor de temperatura: medirá a temperatura do objeto ou do que ele contem.
*   Câmera com leitor de _QR_ code:responsável por ler um _QR_ code que pode conter informações como nome, cor, tamanho, entre outros. Esses dados podem ser comparados com outras informações provenientes dos sensores
*   Sensor ultrassônico: irá medir a altura do objeto, e como base nisso estimar o volume.
*   Detector de presença/movimento): será necessário para garantir o funcionamento dos outros sensores, informando quando o motor da esteira deverá começar ou parar para que os outros sensores possam atuar

##### _Atuadores:_

*   _Display_: Receberá informações dos sensores e as exibirá, fornecendo _feedback_ sobre o estado do sistema ou dos objetos em trânsito na esteira.
*   O Acionamento do motor:Responsável por controlar o movimento da esteira, regulando a velocidade e sentido, e se possível fornecendo uma ideia da velocidade .
*   Braço robótico: ira manipular os objetos, os colocando ou tirando da esteira.

O controle desses dispositivos será descentralizado, com cada um possuindo seu próprio microcontrolador. A base fundamental desse controle será a comunicação entre os dispositivos, realizada principalmente por meio de uma rede utilizando o protocolo _MQTT_.

##### Acionamento do motor

O projeto do Acionamento do motor visa controlar o motor da esteira, incluindo a regulação da velocidade e sentido do movimento. A partida do motor será suave para evitar solavancos, e a esteira terá capacidade de se mover tanto para frente quanto para trás.

#### sequencia de funcionamento

Ao ser detectado um objeto no inicio da esteira, por um sensor, o motor devera ligar, de forma suave, acelerando até atingir uma velocidade aceitável, próximo do meio da esteira, outro sensor devera detectar o objeto se aproximando do meio da esteira e enviar essa informação para que o motor possa começar a diminuir a velocidade, no meio da esteira outros sensores farão as medidas, e essa informação devera ser processada e mostrada no display, com as medidas feitas o motor devera voltar a acelerar até o fim da esteira onde outro sensor próximo do fim informara onde desligar o motor para que o objeto possa ser removido.

#### Tópicos do _MQTT_

_motor:_

*   liga: liga(1) e desliga(0) o motor. publica e recebe.
*   velocidade: recebe a velocidade alvo que deve ser alcançada, em porcentagem.
*   velocidade atual:  publica a velocidade atual, em porcentagem.
*   rampa: tempo para atingir a velocidade maxima, em milissegundos.