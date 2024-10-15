## Esteira PI II

Concebe-se como um projeto para a disciplina de Projeto Integrador 2 do curso de Engenharia Eletrônica do IFSC (Campus Florianópolis) a implementação de uma esteira com diferentes sensores e atuadores acoplados que executam diferentes funções.

Como à uma linha de montagem de uma fábrica, uma esteira deve funcionar de forma que o produto não sofra acelerações ou desacelerações bruscas, garantindo sua integridade até o final da linha. Ao decorrer do caminho o objeto pode sofrer paradas, ou não, para que passe por um processo de controle de qualidade.

Sensores de cor, sensor ultrassom, sensor infravermelho, câmeras e displays serão posicionados ao longo do trajeto para aferir e mostrar valores e proporcionar o bom funcionamento da esteira.

Os dispositivos devem todos ser integrados por meio do protocolo _MQTT_. Tal meio de comunicação é ideal para a integração dos sistemas, visto sua eficiência de comunicação, suporte a conexões instáveis e baixo consumo de energia.

### Leitor _QR Code_

Em determinada parte da esteira um sensor para a captura de imagens será posicionado para a função de ler um Código QR com um texto codificado. O QR impresso deve conter informações a serem aferidas em etapas posteriores da esteira. Quando lido o código, o texto deve ser apresentado no display a ser implementado. As informações contidas devem condizer com a função dos sensores implementados ao longo da esteira, como: cor do objeto, tamanho, profundidade, temperatura.

Os tópicos do _MQTT,_ portanto, a serem utilizados para o Leitor de _QR Code_ são:

*   Objeto -&gt; Após ler o código, publica a informação sobre qual é o objeto tratado em questão.
*   Altura -&gt; Publica a informação sobre a altura real do objeto.
*   Cor -&gt; Publica a informação sobre a cor real do objeto.
*   Temperatura -&gt; Publica a temperatura esperada para o objeto.

### Referências:

DE LUCA, Cristina. Conheça o MQTT, protocolo de mensagens mais usado em IoT. **Network king,** 2022. Disponível em: &lt;[https://network-king.net/pt-pt/conheca-o-mqtt-o-protocolo-de-mensagens-mais-usado-em-iot](https://network-king.net/pt-pt/conheca-o-mqtt-o-protocolo-de-mensagens-mais-usado-em-iot)/&gt;. Acesso em: 20 mar. 2024.

‌