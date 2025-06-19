# Kafka - Um Pouco de Teoria

Apache Kafka é um sistema distribuido, de código aberto, composto por servidores e clientes, usado principalmente para construir pipelines de streaming de dados em tempo real. É uma plataforma de fluxo de eventos, capaz de manipular milhões de mensagens por segundo.

***Um stream de dados é normalmente considerado uma sequência de dados potencialmente ilimitada. O nome streaming é usado porque queremos que os dados sejam acessíveis assim que são produzidos.***

Kafka é muito usado para:
- pipeline de dados de alta performance/integação de dados  
Kafka permite desacoplar as fontes de streams de dados dos consumidores de dados.   

- stream processing  
Processamento de dados em tempo real: aplicação de filtros, joins, maps, agregações, etc...transformações em geral.

- streaming analytics  
Combinado com *Druid* para permitir queries analíticas  

- event-driven microservices  
Facilita comunicação entre microserviçoes, com baixa latência e tolerância a falhas. 

### Não usar

Kafka não deve ser usado como uma ferramenta de filas. Kafka é formado por tópicos, não filas. Filas permitem que milhões de consumidores escalem e as mensagens são deletadas após consumidas. No Kafka os dados não são deletados após serem consumidos e os consumidores não escalam além da quantidade de partições no tópicos. 

Também não deve ser uado como uma base de dados com índices, pois o Kafka não permite queries complexas.

## Conceitos

### Tópicos

Tópicos são como tabelas de dados sem *constraints*. OS tópicos são onde as mensagens são armazenadas, de forma organizada. Pode-se ter quantos tópicos quanto necessário e cada um é idenficiado por um nome. São imutáveis, cada vez que o dado é escrito em uma *partição* não pode mais ser alterado.

#### Partições: tópicos são dividios em partições;
#### Offset: é um *id* incremental, por partição. Não pode ser reaproveitado. Só faz sentido na partição que foi criado.

***A ordem só é garantida dentro de uma mesma partição***
Seum tópico tem mais de uma partição, a ordem é garantida apenas na partição e não através de partições.

### Producers

Um *producer* é uma aplicação que envia dados para os tópicos. 
As mensagens enviadas para um tópico são distribuídas entre as partições de acordo com algum mecanismo baseado nas *keys*. 
Cada mensagem pode possuir uma *key* e um *value*, ambos opcionais.
- Se mensagem não possui uma key (```key == null```), é utilizado o *round-robin* para distribuí-las entre as partições.
- Se as mensagens possuirem uma key, as mensagens que compartilharem de uma mesma key serão sempre enviadas para uma mesma partição. 

#### Mensagem Kafka

![Mensagem Kafka](/img/kafka-message.png)

- compression-type: a mensagem pode ser comprimida; opções: *none*, *gzip*, *lz4*, *snappy* e *zstd*
- headers: um lista opcional no formato *key-value*; usado para metadata, tracing
- partition + offset: número da partição e offset id
- timestamp: gerado pelo usuário ou pelo sistema

#### Message serializer

O Kafka só aceita *bytes* como entrada do producer.
##### Serialization: transforma objetos/dados em bytes, tanto as keys quanto os values.

![Message serializer](/img/kafka-message-serializer.png)

### Consumers

Uma vez que os dados foram produzidos no tópico, podemos criar aplicações para consumí-los.  Essas aplicações são os *consumers*.  São aplicações que leem dados dos tópicos.
- Um *consumer* pode ler dados de uma ou mais partições. Porém, quando está conectado a mais de uma partição, a ordem não é garantida. A ordem apenas é garantida dentro de uma mesma partição. 
- Um *consumer* sempre lê os dados do menos par ao maior offset.
- Por **default**,  o *consumer* só vai ler os dados que foram produzidos após ele ter se conectado ao Kafka. É possível ler dados anteriores (históricos), mas é necessário uma configuração diferente.
- Kafka *consumers* implementam **pull model**:  os *consumers* **DEVEM** pedir (solicitar) os dados ao "kafka broker". Ou seja, o *broker* não fica enviando os dados constantemente (**push model**). Desta forma, o controle da velocidade de consumo fica com o *consumer*.

#### Message Deserializer

Uma vez que os dados foram *serializados* para serem enviados ao *producer*, os mesmos devem ser *deserialozados* para serem enviados ao *consumer*.  Os formatos não devm ser alterados. Se foi usado *StringSerializer*, deve-se usar o *StringDeserializer*, etc.

> **Poison pills** 
>
> Mensagens que não respeitam o acôrdo de formato de serialização.

### Consumer Groups
  
O consumidores dentro de um mesmo grupo são capazes de coordenar a separação do trabalho de leitura de diferentes partições. Qualquer *consumer* faz parte de um *consumer group*.
- cada *consumer* dentro de um grupo lê de partições exclusivas;
- se houver mais *consumers* que partições, esses *consumers* ficarão inativos;

![Consumer group](/img/kafka-consumer-group01.png)


Múltiplos *consumer groups* podem consumir de um mesmo tópico ao mesmo tempo. Cadas *consumer group* possui um ***group.id***. 

![Multiplos consumer groups](/img/kafka-consumer-group02.png)

### Consumer Offsets

Os *brokers* Kafka usam um tópico interno (*__consumer_offsets*) que rastreia as mensagens que um determinado grupo de consumidores processou com sucesso pela última vez.

Regularmente, o consumidor executa o *commit* da última mensagem processada, incrementando o *offset* e gerando um *consumer offset*, funciona como um *chekpoint*.

A maioria das implementações (*libs*) executa o *commit* de forma automática de tempos em tempos (período) e o *broker* escreve no tópico *__consumer_offsets* (Não é o consumer que escreve nesse tópico!!). 

#### Pontos importantes:
- Se algum cliente (*consumer*) tem um problema e pára ou "quebra", ocorre um *rebalance* e o último *offset* comitado indica onde os *consumers* devem reiniciar o processo de leitura;
- Se novos *consumers* são adicionados ao grupo, ocorre um *rebalance* e *offset* indica onde iniciar o processamento.

Por default, consumidores *Java* executam o *commit* automático:
```json
enable.auto.commit=true
```
Ocorre a cada 5s:
```json
auto.commit.interval.ms=5000
```
quando o método ***.poll()*** é chamado.

O consumidor pode escolher quando comitar o *offset*:
```json
enable.auto.commit=false
```






