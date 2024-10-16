&quot;controle\_motor.ino&quot;:

É o arquivo principal, possui as funções de inicialização _setup_(), que ira chamar as funções necessárias para configurar os pinos e a comunicação, nesse arquivo também está as funções responsáveis  por configurar o _wifi_ e o MQTT e suas credenciais, como a função    &quot;setup\_wifi()&quot;   e  &quot;MQTTsetup()&quot;, define também a &quot;reconnect()&quot; que é chamada no _loop_ para reconectar caso a conexão do MQTT seja perdida, (mas não tenta reconectar ao wifi,esse é um problema a ser corrigido), a função que lida com as mensagens recebidas nos topicos subscritos é a &quot;Callback()&quot; e é ela que gerencia os dados como &quot;liga&quot;, &quot;Rampa&quot;, &quot;sentido&quot; e &quot;velocidade&quot;, para o tópico que é publicado existe a &quot;SendCurrentRPM()&quot;, que converte o dado do RPM em uma mensagem que é enviada pelo MQTT. As duas principais funções desse arquivo são a &quot;MotorControl()&quot; e a &quot;CalcRPM()&quot;, elas são responsaveis por controlar quando e de que forma é feita o controle ou uso dos dados vindos do sensor ou acionamento do motor, a &quot;MotorControl()&quot; usa os as variaveis recebidas pelo MQTT para decidir quando e de que forma deve usar a função &quot;acelera()&quot; e a &quot;CalcRPM()&quot; usa o dado que vem da &quot;contRPM&quot; e o converte para RPS, o registra e quando necesario chamaa função para publicar o RPM.

```cpp
#include <WiFi.h>				// Biblioteca para Wi-Fi no ESP32
#include <PubSubClient.h>		// Biblioteca MQTT

#include "acelera.h"
#include "sensor.h"
//MQTT
const char *ssid = "lpae_wifi";
const char *password = "esp-8266";
const char *mqtt_broker = "192.168.1.2";
const char *topic = "/msg";
const int	mqtt_port = 1883;
const char *mqtt_user = "lpae";
const char *mqtt_password = "esp-32";
const char *msg = "Rafael";
WiFiClient espClient;
PubSubClient client(espClient);


//gerenciamento de tempo
unsigned long currentTime;
unsigned long intervalRPM = 1000;	// Intervalo para fazer a media do RPM
unsigned long intervalMrpm = 1000;	// Intervalo para envio e a conta da RPM média
unsigned long intervalControl=100;
unsigned long lastTimeRPM;			// Tempo do último calculo do RPM
unsigned long lastTimeMrpm;			// Tempo da última media do RPM
unsigned long lastTimeControl;


//unsigned long lastTime;			
unsigned long lastReconnectAttempt;	// Tempo da última tentativa de reconexão



//topicos
unsigned int velocidade=0; 			// valor inteiro em porcentagem de 0 a 100 do PWM a ser enviado enviado para o motor 	//topico: string de valores inteiros, deve estar entre "0" e "100".
unsigned int rampa = 5000;			//tempo para atingir o PWM alvo															//topico: string de valores inteiros, deve ser maior que "0".
bool sentido = true;				// Sentido do motor// fazer com que "true" seja o sentido normal						//topico string "1" é "true" o sentido "normal", "0" sentido inverso.
bool liga = false; 					// Estado do motor (ligado/desligado)													//topico: string "1" é ligado "0" desligado, qualquer outra também é desligado.

unsigned int PWM_atual=0;



// Função para conectar ao Wi-Fi
void setup_wifi() {
	delay(10);
	Serial.print("Conectando ao WiFi");
	WiFi.begin(ssid, password);
	
	while (WiFi.status() != WL_CONNECTED) {
		delay(500);
		Serial.print(".");
	}
	
	Serial.println("Conectado ao WiFi");
}

// Função para conectar ao servidor MQTT
void reconnect() {
	if (millis() - lastReconnectAttempt > 5000) { // Tenta reconectar a cada 5 segundos
		lastReconnectAttempt = millis();
		if (!client.connected()) {
			Serial.print("Tentando conectar ao MQTT...");
			if (client.connect("ESP32Client", mqtt_user, mqtt_password)) {
				Serial.println("Conectado");
				client.subscribe("treadmill/commands"); // Assina o tópico de comandos
			} else {
				Serial.print("Falha na conexão, rc=");
				Serial.print(client.state());
				Serial.println(" Tentando novamente em 5 segundos");
			}
		}
	}
}

// Função de callback chamada quando uma mensagem é recebida
void Callback(char* topic, byte* message, unsigned int length) {

	Serial.print("Mensagem recebida no tópico: ");
	Serial.println(topic);
	// Converte a mensagem para uma string
	String msg;
	for (int i = 0; i < length; i++) {
		msg += (char)message[i];
	}
	Serial.print("a mensagem é: ");
	Serial.println(msg);
	// Verifica qual tópico foi recebido
	if (String(topic) == "motor/liga") {
		// Atualiza a variável liga
		liga = (msg == "1");
		Serial.print("Liga: ");
		Serial.println(liga ? "ligando" : "desligando");
	} else if (String(topic) == "motor/velocidade") {
		// Atualiza a variável RPM_alvo
		velocidade = msg.toInt();
		if(velocidade>100){
			velocidade=100;
		};
		if(velocidade<0){
			velocidade=velocidade*-1; //inutill
		};
		Serial.println("oi");
		printf("velocidade em %i %c /n",velocidade,'%');
	} else if (String(topic) == "motor/rampa") {

		rampa = msg.toInt();
		printf("Rampa de %i ms",rampa);
	} else if (String(topic) == "motor/sentido") {
		sentido = (msg == "1");
		Serial.print("Sentido atualizado para: ");
		Serial.println(liga ? "normal" : "inverso");
	}
}
/*
void processaMensagem(String message) {
}
*/
void SendCurrentRPM(float RPM) {
	// Envie o RPM_atual
	char msg[8]; //0.00 - 10000.00
	snprintf(msg, 8, "%.2f", RPM); // Formata o RPM atual em string
	client.publish("motor/RPM", msg); // Envia o RPM atual para o tópico
}

//V=RPM*circu_tambor
void CalcRPM() {
	currentTime = millis();
	
	if (currentTime - lastTimeRPM >= intervalRPM) {
		// Calcula RPM
		float rps = rpmCount*1000.0/(20.0*(currentTime-lastTimeRPM));
		rpmCount = 0; // Zera o contador

		// Armazena o valor no vetor
		rpsValues[indice] = rps;
		indice = (indice + 1) % maxValues; // Move para o próximo índice

		if (indice == 0) { // Se o índice voltar para 0, o vetor está cheio
			isFull = true;
		}
		//printVectoRPM();
		if (currentTime - lastTimeMrpm >= intervalMrpm) {		// A cada "intervalMrpm" a media será feita e enviada pelo MQTT
			printf("RPM: %.2f\n", rps*60);
			SendCurrentRPM(rps*60);
			//float averageRPS = calculateAverageRPS();
			//printf("Average RPM: %.1f\n", averageRPS*60);
			//SendCurrentRPM(averageRPS*60);
			lastTimeMrpm = millis();
		}
		lastTimeRPM = millis();
	}
}

void MotorControl(bool liga,unsigned int &PWM,unsigned int velocidade,unsigned int rampa=5000,bool sentido=true){

	currentTime = millis();

	unsigned int PWM_alvo=(velocidade*255)/100;
	bool printControl=(currentTime-lastTimeControl >= intervalControl);
	if(printControl){
		Serial.print("O PWM alvo é  ");
		Serial.println(PWM_alvo);
		Serial.print("O PWM atual é ");
		Serial.println(PWM);
		intervalControl=100;
	}
	if(liga&&(PWM_alvo!=PWM)){
		Serial.println("ligado");
		PWM = acelera(PWM,PWM_alvo,rampa);
	}
	else if(liga&&(printControl)){
		Serial.println("mantendo a velocidade");
		intervalControl=500;
	}
	else if(!liga&&(PWM!=0)){
		Serial.println("desligando");
		PWM = acelera(PWM,0,2000);
	}
	else {;
		if(printControl){
			Serial.println("desligado");
			intervalControl=1000;
		}
	}
	if(printControl){
		lastTimeControl=millis();
	}
}

void MQTTsetup(){
	client.setServer(mqtt_broker, mqtt_port);
	while (!client.connected()) {

		String client_id = "esp32-client-"; // Cria uma string pro id do cliente MQTT
		client_id += String(WiFi.macAddress()); // Gera uma id específica com base no endereço MAC da esp32 específica
		Serial.printf("The client %s connects to the public MQTT broker\n", client_id.c_str()); // Imprime uma mensagem indicando que o cliente está tentando se conectar ao broker MQTT

		if (client.connect(client_id.c_str(), mqtt_user, mqtt_password)) { // Tenta conectar o cliente MQTT ao broker MQTT usando o ID do cliente, nome de usuário e senha especificados
			Serial.println("Public EMQX MQTT broker connected"); // Em caso de conexão bem sucedida, imprime uma mensagem indicando que o cliente está conectado ao broker

			client.subscribe("motor/liga");
			client.subscribe("motor/velocidade");
			client.subscribe("motor/rampa");
			client.subscribe("motor/sentido");
			
			client.setCallback(Callback);
		} else {

			Serial.print("failed with state "); // Caso haja falha na conexão, envia uma mensagem de falha

			Serial.print(client.state()); // Imprime o estado atual do cliente MQTT

			delay(2000);
		}
	}
}

void setup() {
	Serial.begin(115200);
	Serial.println("Incio");
	
	pinMode(sensorPin, INPUT_PULLUP);
	MotorSetup();
	
	setup_wifi();
	MQTTsetup();

	attachInterrupt(digitalPinToInterrupt(sensorPin), countRPM, RISING);
	currentTime	= millis();
	lastTimeRPM	= currentTime;
}

void loop() {
	if (!client.connected()) {
		reconnect(); // Tenta reconectar ao MQTT
	}
	client.loop(); // Processa as mensagens MQTT
	MotorControl(liga,PWM_atual,velocidade,rampa);//controla o motor
	//AceleraTeste(PWM_atual);
	CalcRPM(); // mede o RPM
	
	//SendCurrentRPM(); é enviado dentro de CalcRPM por enquanto
}
```

### &quot;acelera.h&quot;

arquivo relacionado ao controle do motor,

```cpp
//pinos para controlar o sentido do motor
const int PINO_ENA = 13; //d13 Controle do PWM do motor A,sinal PWM 0 a 255, controla 0% a 100% em relação ao duty cycle
const int PINO_IN1 = 12; //d12
const int PINO_IN2 = 14; //d14

const int TEMPO_ESPERA = 1000; 

// Configurações PWM // não são usados
const int pwmChannel = 0;
const int pwmResolution = 8; // 8 bits de resolução PWM
const int pwmFrequency = 1000; // Frequência do PWM em Hz

int TEMPO_RAMPA = 20;				//Tempo para incrementar em "incremento" o PWM

// Função para acelerar e desacelerar o motor
unsigned int acelera(unsigned int PWM_atual, unsigned PWM_alvo, unsigned int rampa = 5000, bool sentido = true, unsigned int incremento = 1) {
	// Configura o sentido do motor
	int PWM_inical=PWM_atual;
	if (sentido) {
		digitalWrite(PINO_IN1, LOW); 
		digitalWrite(PINO_IN2, HIGH);
	} else {
		digitalWrite(PINO_IN1, HIGH); 
		digitalWrite(PINO_IN2, LOW);
	}
	unsigned long tempo_total = 0;
	// aceleração ou desaceleração
	if (PWM_atual < PWM_alvo) {
		Serial.println("Acelereando");
		TEMPO_RAMPA = rampa*incremento/(255);
		printf("O tempo do incremento é %i \n",TEMPO_RAMPA);
		for (; PWM_atual < PWM_alvo; PWM_atual += incremento) {
			if (PWM_atual > 255) {PWM_atual = 255;};	// Limita o valor máximo
			analogWrite(PINO_ENA, PWM_atual);
			printf("PWM em %i\n", PWM_atual);
			delay(TEMPO_RAMPA); // Intervalo para incrementar a variável
			//tempo total =delay*(PWM_alvo-PWM_inical)/incremento=rampa
			tempo_total += TEMPO_RAMPA;
		}
	} else {
		Serial.println("Descelereando");
		TEMPO_RAMPA = rampa*incremento/(255);
		printf("O tempo do incremento é %i \n",TEMPO_RAMPA);
		for (; PWM_atual > PWM_alvo; PWM_atual -= incremento) {
			if (PWM_atual > 255) {PWM_atual = 0;};	// Limita o valor mínimo
			analogWrite(PINO_ENA, PWM_atual);
			printf("PWM em %i\n", PWM_atual);
			delay(TEMPO_RAMPA); // Intervalo para diminuir a variável
			tempo_total += TEMPO_RAMPA;
		}
	}
	// Imprime o tempo total de aceleração/desaceleração
	Serial.print("Tempo total de ");
	Serial.print(sentido ? "aceleração" : "desaceleração");
	Serial.print(": ");
	Serial.print(tempo_total);
	Serial.println(" ms");

	return PWM_atual;
}

void MotorSetup(){
	// Configuração do PWM //não consegui usar
	//ledcSetup(pwmChannel, pwmFrequency, pwmResolution);
	//ledcAttachPin(PINO_ENA, pwmChannel);
	pinMode(PINO_ENA, OUTPUT);
	pinMode(PINO_IN1, OUTPUT);
	pinMode(PINO_IN2, OUTPUT);
}
void AceleraTeste(unsigned int &PWM) {
	unsigned int rampaTeste=5000;
	// Acelera até o máximo
	Serial.println("indo de 0 a 100%");
	PWM = acelera(PWM, 255, rampaTeste, true);
	delay(TEMPO_ESPERA);
	
	// Desacelera até zero
	Serial.println("indo de 100% a 0");
	PWM = acelera(PWM, 0, rampaTeste, true);
	delay(TEMPO_ESPERA);
	
	// Acelera até o máximo no novo sentido
	Serial.println("indo de 0 a 100%");
	PWM = acelera(PWM, 255, rampaTeste, false);
	delay(TEMPO_ESPERA);
	// Desacelera até zero no novo sentido
	Serial.println("indo de 100% a 0");
	PWM = acelera(PWM, 0, rampaTeste, false);
	delay(TEMPO_ESPERA);
}
```

### &quot;sensor.h&quot;

É o arquivo que gerencia o sensor do RPM, define a função que é chamada sempre que há um pulso emitido pelo sensor &quot;countRPM()&quot;, que incrementa em 1 a variável &quot;rpmCount&quot;, essa função é chamada através de interrupção, sendo chamada no momento que há esse pulso do sensor independente de que parte do codigo está sendo executado, a função &quot;setupSensor()&quot; que configura o pino do sensor e configura a forma que a função&quot;countRPM()&quot; é chamada, a função &quot;calculateAverageRPS()&quot; é usada para fazer a media do vetor dos últimos RPM medidos e a &quot;printVectoRPM&quot; imprime esse vetor para caso seja necessário checar os verdadeiros valores das medições.

```cpp
// sensor
const int sensorPin = 2;			// Pino do sensor
volatile int rpmCount = 0;			// Contador de pulsos de RPM

//Calculo do RPM
const int maxValues = 10; 			// Número máximo de valores a armazenar
float rpsValues[maxValues];			// Vetor para armazenar valores de RPS
int indice = 0;						// Índice para o vetor circular
bool isFull = false;				// Flag para indicar se o vetor está cheio




void countRPM() {
	// Incrementa o contador de RPM a cada pulso
	rpmCount++;
}

float 		 {
	// Calcula a média dos valores armazenados
	float sum = 0;
	int count = isFull ? maxValues : indice;
	for (int i = 0; i < count; i++) {
		sum += rpsValues[i];
	}
	return sum / count;
}

void printVectoRPM() {
    // Determina o número de valores a imprimir
    int count = isFull ? maxValues : indice;

    // Imprime os valores válidos
    printf("RPM values:\n");
    for (int i = 0; i < count; i++) {
        printf("%.1f ", i, rpsValues[i]);
    }
    Serial.println("");
}

void setupSensor() {
	pinMode(sensorPin, INPUT_PULLUP);
	attachInterrupt(digitalPinToInterrupt(sensorPin), countRPM, RISING);
}
```