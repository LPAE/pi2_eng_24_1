**Origem**

O termo Indústria 4.0 iniciou na Alemanha, com o conceito sendo introduzido pela primeira vez na Feira de Hannover (1), em 2011. Desde então, tem se espalhado globalmente, ganhando destaque em diversas indústrias e setores econômicos.

A Indústria 4.0 representa uma revolução tecnológica que está transformando profundamente a forma como as empresas produzem. Este conceito refere-se à integração de tecnologias digitais avançadas, como Internet das Coisas (IoT), inteligência artificial, big data, computação em nuvem e robótica, no ambiente industrial (2).

A conectividade é o principal fator, onde dispositivos físicos são interligados através da internet, permitindo a coleta e análise de dados em tempo real para otimização de processos, aumento da eficiência entre outros benefícios. A IoT desempenha um papel fundamental nesse contexto, fornecendo a infraestrutura necessária para conectar máquinas, sensores e outros dispositivos à rede.

**Eletrônica e Indústria 4.0**

A eletrônica desempenha um papel fundamental na Indústria 4.0, fornecendo as bases tecnológicas necessárias para a transformação digital e a automação dos processos industriais. Por exemplo a conectividade de dispositivos eletrônicos, (sensores, atuadores, controladores e sistemas embarcados) que permitem obtencão de dados, processamento e troca de dados em tempo real, possibilitando uma maior eficiência, redução de custos e tomada de decisões na indústria.

Sensores eletrônicos são utilizados para monitorar temperatura, umidade, pressão, nível de fluidos e presença de objetos entre outros. Esses dados são então transmitidos para controladores eletrônicos, que podem tomar decisões autônomas ou fornecer informações para sistemas de supervisão e controle.

Sistemas embarcados desempenham um papel vital na Indústria 4.0 no controle, análise de dados e tomada de decisões em tempo real. Plataformas como o ESP32 são amplamente utilizadas devido à sua capacidade de conectividade Wi-Fi e Bluetooth, baixo consumo de energia (3).

**ESP32**

O ESP32 é uma família de microcontroladores de baixo custo e baixo consumo de energia desenvolvida pela empresa chinesa Espressif Systems. Ele é amplamente conhecido por sua capacidade de conectividade Wi-Fi e Bluetooth integrada, tornando-o uma escolha popular para uma variedade de aplicações IoT (Internet das Coisas) e projetos de eletrônica.

Alguns dispositivos de comunicação utilizados na Industria para obtenção de dados de sensores e tomadas de decisão são baseados em ESP32 por possuir algumas vantagens tais como (4):

*   **Conectividade Avançada**: O ESP32 possui capacidade de conectividade Wi-Fi e Bluetooth.
*   **Baixo Consumo de Energia**: Isso permite que os dispositivos baseados em ESP32 operem por longos períodos com baterias ou fontes de energia limitadas.
*   **Processamento de Dados**: Isso é fundamental para a execução de algoritmos de análise de dados e tomada de decisões locais em tempo real.
*   **Flexibilidade de Programação**: O ESP32 é altamente flexível em termos de programação, suportando diversas linguagens e ambientes de desenvolvimento, como Arduino IDE, MicroPython e ESP-IDF.
*   **Custo Acessível**: O ESP32 é uma solução de baixo custo em comparação com outras plataformas de hardware semelhantes.

Abaixo estão ilustrados os periféricos internos ao microcontrolador ESP32:

<figure class="image op-uc-figure" style="width:32.14%;"><div class="op-uc-figure--content"><img class="op-uc-image" src="/api/v3/attachments/3285/content"></div><figcaption class="op-uc-figure--description">Componentes internos ESP32 (Fonte: MakerHero)</figcaption></figure>

**MQTT**

Protocolos de comunicação eletrônicos são conjuntos de regras e convenções que permitem a troca de informações entre dispositivos eletrônicos de forma organizada e padronizada. Esses protocolos estabelecem diretrizes sobre como os dados devem ser formatados, transmitidos, recebidos e interpretados durante a comunicação entre dispositivos.

MQTT (Message Queuing Telemetry Transport) é um protocolo de mensagens leve e eficiente projetado para a troca de mensagens entre dispositivos em redes de comunicação de baixa largura de banda e alta latência (5).

O MQTT opera em um modelo de publicação/assinatura, onde os dispositivos podem publicar mensagens em tópicos específicos e se inscrever para receber mensagens de tópicos de interesse. Essa abordagem permite uma comunicação assíncrona entre os dispositivos, onde os dados podem ser enviados e recebidos de forma eficiente e sem a necessidade de uma conexão direta entre remetente e destinatário.

As principais características do MQTT incluem: Leveza; Eficiência; Confiabilidade; Escalabilidade; Flexibilidade.

Diagrama de funcionamento do protocolo MQTT:

<figure class="image op-uc-figure" style="width:24.27%;"><div class="op-uc-figure--content"><img class="op-uc-image" src="/api/v3/attachments/3286/content"></div><figcaption class="op-uc-figure--description">Diagrama básico comunicação MQTT (Fonte: SpiceWorks)</figcaption></figure>

**Sensores Ultrassônicos**

O uso de sensores ultrassônicos na Indústria 4.0 é extremamente relevante devido às suas capacidades de detecção precisa e versatilidade em uma variedade de aplicações industriais. Esses sensores funcionam emitindo pulsos de ultrassom e medindo o tempo que leva para os sinais retornarem após serem refletidos por um objeto. Exemplos de uso: Monitoramento de Nível de Fluidos; Detecção de Objetos; Medição de Distância e Posicionamento; Inspeção de Qualidade, etc (6).

Os sensores ultrassônicos funcionam com base no princípio da eco localização, que é semelhante ao sistema utilizado por morcegos e golfinhos para navegar e detectar objetos em seu ambiente. Aqui está uma explicação básica de como os sensores ultrassônicos funcionam (8):

*   Emissão de ondas ultrassônicas: O sensor emite pulsos de ondas ultrassônicas em direção ao objeto que se deseja detectar ou medir a distância. Essas ondas ultrassônicas são emitidas em frequências muito altas, geralmente acima de 20 kHz, o que é além do alcance da audição humana.
*   Reflexão do som: Quando as ondas ultrassônicas encontram um objeto em seu caminho, parte delas é refletida de volta para o sensor devido à diferença na impedância acústica entre o ar e o objeto. Quanto mais próximo o objeto estiver do sensor, mais rápido o eco retornará.
*   Medição do tempo de retorno: O sensor calcula o tempo decorrido entre a emissão do pulso ultrassônico e o recebimento do eco refletido. Conhecendo a velocidade do som no ar (que é aproximadamente 343 metros por segundo a 20 graus Celsius), o sensor pode determinar a distância até o objeto utilizando a fórmula da velocidade média: distância = velocidade x tempo.
*   Conversão em dados de distância: Com base no tempo de retorno medido, o sensor fornece uma leitura que representa a distância entre ele e o objeto detectado. Esses dados podem ser então utilizados para diversas finalidades, como controle de movimento, detecção de obstáculos, medição de nível de líquidos, entre outros.

<figure class="image op-uc-figure" style="width:45.28%;"><div class="op-uc-figure--content"><img class="op-uc-image" src="/api/v3/attachments/3287/content"></div><figcaption class="op-uc-figure--description">Funcionamento básico sensores Ultrassonicos (Fonte: &nbsp;Canal do YouTube XYZ(9))</figcaption></figure>

**REFERÊNCIAS:**

\[1\] DEUTSCHLAND. Indústria 4.0 na feira de Hanover. Disponível em: &lt;https://www.deutschland.de/pt-br/topic/economia/globalizacao-comercio-mundial/industria-40-na-feira-de-hanover\\&gt;. Acesso em: 27 fev. 2023.

\[2\] LOGAP. Principais tecnologias da Indústria 4.0. Disponível em: &lt;https://logap.com.br/blog/principais-tecnologias-industria-4-0/\\&gt;. Acesso em: 27 fev. 2023.

\[3\] EMBARCADOS. Indústria 4.0. Disponível em: &lt;https://embarcados.com.br/industria-4-0/\\&gt;. Acesso em: 27 fev. 2023.

\[4\] MAKERHERO. ESP32: um grande aliado para o maker IoT. Disponível em: &lt;https://www.makerhero.com/blog/esp32-um-grande-aliado-para-o-maker-iot/\\&gt;. Acesso em: 27 fev. 2023.

\[5\] UFRJ. MQTT - Message Queuing Telemetry Transport. Disponível em: &lt;\[https://www.gta.ufrj.br/ensino/eel878/redes1-2019-1/vf/mqtt/#:~:text=MQTT(Message%20Queuing%20Telemetry%20Transport,cima%20do%20protocolo%20TCP%2FIP.\\&gt;\](https://www.gta.ufrj.br/ensino/eel878/redes1-2019-1/vf/mqtt/#:~:text=MQTT(Message%20Queuing%20Telemetry%20Transport,cima%20do%20protocolo%20TCP%2FIP.%5C%3E). Acesso em: 27 fev. 2023.

\[6\] UNICREA. Sensores ultrassônicos: alta tecnologia para a indústria. Disponível em: &lt;https://unicrea.crea-sc.org.br/blog/post/sensores-ultrassonicos-alta-tecnologia-para-a-industria\\&gt;. Acesso em: 27 fev. 2023.

\[7\] SPICEWORKS. What is MQTT? Disponível em: [https://www.spiceworks.com/tech/iot/articles/what-is-mqtt/](https://www.spiceworks.com/tech/iot/articles/what-is-mqtt/). Acesso em: 27 fev. 2023.

\[8\] SENSE. Sensor ultrassônico. Disponível em: [https://www.sense.com.br/siter/familias/298](https://www.sense.com.br/siter/familias/298). Acesso em: 27 fev. 2023.

\[9\] Como Funciona um Sensor Ultrassônico? | HC-SR04. Autor: Canal do YouTube XYZ. Disponível em: &lt;https://www.youtube.com/watch?app=desktop&amp;v=Y8nSAxaPKCE\\&gt;. Acesso em: 27 fev. 2023.