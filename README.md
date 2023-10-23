# Sensores e Atuadores Básicos

## Objetivos

Nesse encontro, teremos a programação básica com circuito RC, entradas por meio de botão e saídas informacionais simples (Leds). 

Os alunos devem realizar a coleta de dados de um experimento montado pelo professor sobre um circuito RC para filtragem e preencher um gráfico. 

O relatório deve ser produzido em sala e entregue ao professor no final da instrução.

## Assuntos relacionados

- Fundamentos de controle e automação
- Introdução, histórico e aplicações de sistemas embarcados
- Programação de microcontroladores
- Simulação de sistemas embarcados
- Uso de ferramentas computacionais para projeto, simulação e análise

## Vantagens dessa instrução para o seu projeto

É provável que seu projeto usará botões de pressão como entrada de dados. 

Por exemplo, pressione botão A para mover as opções do menu, pressione B para selecionar uma opção do menu, etc.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/botoes.png">
   <img alt="Boucing" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/botoes.png)">
</picture>

E ao pressionar o botão, microscopicamente há várias pressionadas devido às imperfeições das chapinhas metálicas de contatos internos, gerando o sinal que se nota na imagem a seguir.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/boucing.png">
   <img alt="Boucing" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/boucing.png)">
</picture>

**ESSE EFEITO CHAMA-SE BOUNCING (REPIQUE)**


Nota-se que o **nível alto** do sinal foi gerado mais de uma vez, e isso confunde o programa que possui um loop rápido de leitura única, resultando na detecção involutária de várias pressionada.

O prejuízo é que o seu programa vai assumir que você pressionou mais de uma vez e vai avançar o processo sem você querer. Veja a imagem a seguir que mostra, através de uma tela de um osciloscópio, o comportamento de um botão sendo pressionado com bouncing.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/boucing_oscilas.png">
   <img alt="Bouncing Osciloscópio" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/boucing_oscilas.png)">
</picture>


### Pergunta: Como evitar esse problema? Por meio de 2 formas.

### (1) Usando hardware, por meio de um componente eletrônico chamado Capacitor

### (2) Usando software, por meio de uma rotina usando a função ```milis()```



### (1) Usando hardware, por meio de um componente eletrônico chamado Capacitor

O capacitor é um componente eletrônico bem antigo e super importante. A função dele será **alisar** os repiques microscópicos do botão.

A figura a seguir mostra a carinha do capacitor.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/capacitores.png">
   <img alt="Capacitores" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/capacitores.png)">
</picture>

O capacitor possui 2 placas internas condutoras e separadas por um **dielétrico**, conforme mostra a figura a seguir.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/capacitores_interna.jpg">
   <img alt="Capacitor Interno" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/capacitores_interna.jpg)">
</picture>

Um **dielétrico** é um material isolante. Mas devido à alta quantidade de cargas numa mesma área, esse material isolante não segura a pressão das cargas, e o resultado é a fluidez de tais cargas. Esse fluxo de cargas dá-se o nome de **corrente elétrica**.

Portanto, a função do capacitor é basicamente transferir a variação de cargas elétricas de uma placa para a outra, e no processo dessa transferência ocorre um alisamento da variação. 

Reforçando: **precisa haver a variação da carga ao longo de um intervalo de tempo para que a corrente elétrica circule dentro do capacitor**.

Portanto, só repetindo: a quantidade de carga elétricas numa dada área é o mesmo que **tensão elétrica** e a **corrente elétrica** é a movimentação dessas cargas num dado instante de tempo.

O capacitor possui basicamente 2 aplicações:

1) Filtragem de sinais alternados ou pulsantes.
2) Armazenamento de cargas elétricas.
 
## Voltando ao seu projeto novamente

Recomenda-se adotar a aplicação (1) no seu projeto para o seu código-fonte ser mais simples e ao mesmo tempo, contornar o efeito indesejado do bouncing. 

A aplicação (2) pode ser uma alternativa quando você deseja simplificar seu hardware, já que seu código-fonte também pode resolver o bouncing.

Hoje veremos as duas aplicações.


# Anti-bouncing usando Hardware

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/anti_boucing.png">
   <img alt="Anti Bouncing" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/anti_boucing.png)">
</picture>


```
unsigned long time; // váriavel para receber valores da função milli()
int cont = 0; // o contador de variações
int entrada = 7;
bool estado = LOW; // variável de estado do botão
int aux = 1;


void setup() {
  Serial.begin(9600);
  pinMode(led,OUTPUT);
  pinMode(entrada,INPUT);
  time = millis(); //iniciamos a variável com o primeiro valor do "cronometro"
}
void loop() {

  estado = digitalRead(entrada);
  if (estado==HIGH && aux==1){
    cont++;
    aux = 0;
  }
  if (estado==LOW && aux==0){
    cont++;
    aux = 1;
  }
  if (millis()-time>=500){ // imprime o número de variações a cada 0,5 segundo
    time=millis(); // guarda o novo ponto de inicio para a proxima analise
    Serial.print("variações = ");
    Serial.print(cont);
    Serial.print("\n");
    cont = 0;
   }    
}
```

# Anti-bouncing usando Software

```
int led = 4;          //declara a variável led igual a 4
int entrada = 7;      //declara a variável entrada igual a 7
int aux = 1;          //declara a variável aux igual a 1
int cont = 0;         //declara a variável cont igual a 0
bool estado = LOW;    //variável de estado do botão inicia com nível baixo
unsigned long time;   //variável time tipo long para a função millis()

void setup() {      //estrutura básica de qualquer arduino, chamada void setup()
   pinMode(led,OUTPUT);      //especifica que o pino led é do tipo saída
   pinMode(entrada,INPUT);   //especifica que a variável entrada é do tipo entrada
   Serial.begin(9600);      //inicia a comunicação serial para vc imprimir dados nela usando Serial.print()
   time = millis();         //grava o valor do contador interno chamado milis na variável time
}

void loop() {            //estrutura básica de qualquer arduino, chamada void loop()
  
   do{                  //faça ... enquanto ...
      estado_anterior = digitalRead(entrada);   //faz uma leitura digital no pino entrada e guarda em estado_anterior
      delay(50);                                 //aguarda 50 milisegundos
      estado = digitalRead(entrada);            // novamente faz uma leitura do pino entrada
   }while(estado_anterior != estado);  //aqui definimos um intervalo de tempo de segurança usando a função delay()
                                       //para sair do loop é necessário identificar uma constancia no estado do botão, ele precisa ser o mesmo durante 50 ms
   
   if (estado==HIGH && aux==1){    //testa se estado é nível alto E aux é igual a 1
      aux = 0;                     //grava 0 em aux
      cont++;                      //incrementa em 1 a variável cont
      digitalWrite(led,HIGH);      //aciona-se um LED com nível alto
   }
   
   if (estado==LOW && aux==0){     //testa se estado é nível baixo e aux é igual a 0
      aux = 1;                     // grava 1 em aux
      cont++;                      //incrementa em 1 a variável cont
      digitalWrite(led,LOW);       //desliga o LED com nível baixo
   }
   
      if (millis()-time>=500){     //imprime o número de variações a cada 0,5 segundo
         time=millis();            //guarda o novo ponto de início para a próxima análise
         Serial.print("variações = ");   //imprime o texto entre aspas duplcas
         Serial.print(cont);             //imprime conteúdo da variável cont
         Serial.print("\n");             //puxa uma linha
         cont = 0;                       //grava 0 na variável cont
      }
   }
   ```

# Comportamento Gráfico do C

Você pode reparar que o circuito do Capacitor C está em série com o resistor R. Todo circuito antibouncing é assim: C em série com R. Logo mais, você verá uma prática disso.

Quando você pressiona o botão de pressão, a tensão interna no C vai se comportar da seguinte forma:

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/cargaC.png">
   <img alt="Carga e Descarga do C" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/cargaC.png)">
</picture>

A tensão começa em 0V e vai aumentando até o valor máximo de E que é o valor de VCC (VCC é o valor da tensão que o circuito trabalha). Por exemplo, se uma malha trabalhar com 3,3V, o capacitor vai carregar até 3,3V. Se a malha trabalha com 5V, vai carregar até 5V. Uma malha é o circuito fechado em que se encontra o capacitor C.

## Quando fecha a chave

Quando você fecha a chave, acontece isso conforme a figura a seguir. O capacitor começa do 0V e vai subindo rapidamente e a corrente I dá um pico e começa cair.

Como a corrente I do capacitor é igual no resistor, pois estão em série, o I do R também vai dar um pico e começar a cair.

**Logo, o que acontece no C e no R em termos de corrente I, são iguais em comportamento**.

Esse tempo de subida do V e do I depende da combinação dos valores entre C e R. O intervalor inteiro da curva do C é calculadora como sendo T = 1 / R*C. Esse T chama-se TAL e curva gasta sempre 5T. No 6ºT, a curva praticamente já se estabilizou.


<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/cargaC-parte1.png">
   <img alt="Carga - parte 1" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/cargaC-parte1.png)">
</picture>

Quando a tensão de C vai se aproximando do valor máximo E e a corrente I vai se aproximando do mínimo 0A, a curva se torna mais suave.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/cargaC-parte2.png">
   <img alt="Carga - parte 2" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/cargaC-parte2.png)">
</picture>

## Quando abre a chave

Quando você abre a chavinha de pressão, ocorre o contrário: o C que está cheio começa a se descarregar e a corrente que era 0A, volta a circular lentamente até atingir seu valor máximo. 

Como nos interessa a análise somente da tensão V no capacitor, veja o gráfico abaixo de como o C se comporta a cada vez que você pressiona a chavinha do seu projeto. Já a corrente I do capacitor, será analisada no R na parte prática, e como o comportamento de I e V são iguais no R, então, vamos analisar a tensão V também no R.

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/gabarito%20da%20simula%C3%A7%C3%A3o.png">
   <img alt="Carga - parte 2" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/gabarito%20da%20simula%C3%A7%C3%A3o.png)">
</picture>

**Fique atento que esse é o gabarito da prática de hoje**.

# Prática com circuito RC

## Circuito TinkerCad

Por favor, acessem o [TinkerCad](https://www.tinkercad.com/dashboard), entre com a sua conta Google / Estudante. Abra um novo projeto e monte o circuito a seguir:

<picture>
   <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/circuitoRCTinkerCad.png">
   <img alt="Ciruico Prático TinkerCad" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/circuitoRCTinkerCad.png)">
</picture>


Lista de peças:

- 01 protoboard
- 01 Arduino Uno R3
- 01 C de 10uF e 25V
- 01 R de 1M ohms (marrom, preto, verde)
- 01 R de 100 ohms (marrom, preto, marrom)
- 01 interruptor deslizante
- fios

## Seu objetivo nessa prática será:

1) Montar o circuito no [TinkerCad](https://www.tinkercad.com/dashboard) 
2) Colar o código-fonte abaixo no Arduino Uno
3) Ligar a chavinha
4) Capturar os dados de tensão do C e do R pelo Monitor Serial do TinkerCad
5) Copiar e colar esses valores para o Colab
6) Montar um gráfico
7) Confirmar o compartamento da tensão do C e do R

```
int pinoNoRC=0; 
int valorLido = 0;
float tensaoCapacitor = 0, tensaoResistor;
unsigned long time; 
void setup(){ 
	Serial.begin(9600); 
} 
void loop() { 
	time=millis(); 
	valorLido=analogRead(pinoNoRC); 
	tensaoResistor=(valorLido*5.0/1023); // 5.0V / 1023 degraus = 0.0048876 
	tensaoCapacitor = abs(5.0-tensaoResistor);
 	Serial.print(time); //imprime o conteúdo de time no MONITOR SERIAL
    	Serial.print(" "); 
  	Serial.print(tensaoResistor);
  	Serial.print(" ");
  	Serial.println(tensaoCapacitor); 
	delay(400); 
}

```
Etapas:

1) Abra o [TinkerCad](https://www.tinkercad.com/dashboard) e selecione a opção **Projetos** no menu vertical esquerdo, e depois clique em **Criar** e depois **Circuito**.
   
2) Puxe um protoboard para a área em branco de desenvolvimento.
   
4) Puxe um Arduino Uno R3, copie e cole o código nele.
   
5) Puxe os componentes e seus valores e faça as ligações eletrônicas indicadas no desenho já demonstrado. Você pude mudar as cores dos fios na barra horizontal superior.

   <picture>
      <source media="(prefers-color-scheme: light)" srcset="https://github.com/agodoi/m4-semana2a/blob/main/imgs/circuitoRCTinkerCad.png">
      <img alt="Ciruico Prático TinkerCad" src="[YOUR-DEFAULT-IMAGE](https://github.com/agodoi/m4-semana2a/blob/main/imgs/circuitoRCTinkerCad.png)">
   </picture>

6) Clique em **Iniciar simulação**
   
8) Clique no botão **Código** e na parte de baixo, clique em **Monitor Serial**. Expanda essa tela ao máximo usando o seu mouse para você visualizar o máximo de valores. E terá os seguintes valores:


|tempo (ms)|Tensão R|Tensão C|
|--|--|--|
|0|5.00|0.00|
|401|4.80|0.20|
|803|4.61|0.39|
|1206|4.43|0.57|
|1607|4.26|0.74|
|sua vez| ... | ...|

9) Simule o circuito até 9,6s, copie os dados usando o **botão direito do mouse**, o teclado não funciona.
10) Cole no **Bloco de notas** para manter a formatação dos dados.
11) Agora copie e cole e passe para Colab e use esse código

```
import matplotlib.pyplot as plt

# Seus dados
dados = [
    (0, 5.00, 0.00),
    (401, 4.80, 0.20),
    (803, 4.61, 0.39),
 mantenha esse formato e adicione seus dados do MONITOR SERIAL
]

# Separando os dados
x = [item[0] for item in dados]
y1 = [item[1] for item in dados]
y2 = [item[2] for item in dados]

# Criando o gráfico
plt.figure(figsize=(10, 6))
plt.plot(x, y1, label='Y1')
plt.plot(x, y2, label='Y2')

# Configurando rótulos e título
plt.xlabel('X')
plt.ylabel('Valores')
plt.title('Gráfico de Dispersão')
plt.legend()

# Exibindo o gráfico
plt.grid(True)
plt.show()
```

12) Salve o gráfico porque você vai precisar na ponderada de hoje.

## Desafio!

Faça essa simulação caso você tenha terminado com folga a prática anterior.

Tente acionar o LED_BUILTIN no seu código-fonte e faça-o piscar a cada impressão de dados no MONITOR SERIAL. O LED_BUILTIN é nativo da placa Arduino. Ele pisca em verde na própria placa Arduino IDE. Não são todas as placas que possuem LED nativo. Por exemplo, o ESP32 que você vai usar no lab não tem. Nesse caso, teria que adicionar um LED com resistor R em série (use 180 ohms).

O objetivo desse desafio é unir a extração de dados com um indicador de saída de dados através do piscar de um LED. Ele é um **atuador** pois executa uma função de lhe informar que os dados estão saindo.

Se você terminar e tiver sucesso, chame o professor na sua bancada e ganhará um prêmio!


# Conclusões

- Como o capacitor é lerdo para carregar, o bouncing da chave é cancelado. O capacitor alisa a variação do sinal provocado pela imperfeição interna dos contados da chavinha.

E mais:

- Você viu como o capacitor se comporta ao carregar e descarregar.
- No desafio, tem um exemplo de atuador visual, que é o LED nativo da placa, que pisca a cada vez que o Monitor Serial imprime um novo dado.

  **Provavelmente você usará isso no seu projeto do hospital** Fique atento!
