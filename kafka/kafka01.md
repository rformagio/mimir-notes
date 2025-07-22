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

Tópicos são *streams* de dados. Também pode-se "compará-los" com tabelas de dados sem *constraints*. OS tópicos são onde as mensagens são armazenadas, de forma organizada. Pode-se ter quantos tópicos quanto necessário e cada um é idenficiado por um nome. São imutáveis, cada vez que o dado é escrito em uma *partição* não pode mais ser alterado.

#### Partições: tópicos são dividios em partições;
#### Offset: é um *id* incremental, por partição. Não pode ser reaproveitado. Só faz sentido na partição que foi criado.

***A ordem só é garantida dentro de uma mesma partição***  
Se um tópico tem mais de uma partição, a ordem é garantida apenas na partição e não através de partições.

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
- partition: número da partição  
- offset: offset id
- timestamp: gerado pelo usuário ou pelo sistema

#### Message serializer

O Kafka só aceita *bytes* como entrada do producer.
##### Serialization: transforma objetos/dados em bytes, tanto as keys quanto os values.

![Message serializer](/img/kafka-message-serializer.png)

>> No nível do código, após a montagem do *record* da mensagem, o método *.send()* repassa o *record* para um bloco conehcido como *partitioner*. Esse bloco é responsável em determinar para qual partição será enviada a mensagem. O processo de mapear para qual partição será enviada a mensagem utilizando a *key* é chamado de *Key Hashing*.  No *partitioner* padrão, as *keys* são transformadas em *hash* usando o ***murmur2*** algorithm  

### Consumers

Uma vez que os dados foram produzidos no tópico, podemos criar aplicações para consumí-los.  Essas aplicações são os *consumers*.  São aplicações que leem dados dos tópicos.
- Um *consumer* pode ler dados de uma ou mais partições. Porém, quando está conectado a mais de uma partição, a ordem não é garantida. A ordem apenas é garantida dentro de uma mesma partição. 
- Um *consumer* sempre lê os dados do menor para o maior offset.
- Por **default**,  o *consumer* só vai ler os dados que foram produzidos após ele ter se conectado ao Kafka. É possível ler dados anteriores (históricos), mas é necessário uma configuração diferente.
- Kafka *consumers* implementam **pull model**:  os *consumers* **DEVEM** pedir (solicitar) os dados ao "kafka broker". Ou seja, o *broker* não fica enviando os dados constantemente (**push model**). Desta forma, o controle da velocidade de consumo fica com o *consumer*.

#### Message Deserializer

Uma vez que os dados foram *serializados* para serem enviados ao *producer*, os mesmos devem ser *deserialozados* para serem enviados ao *consumer*.  Os formatos não devem ser alterados. Se foi usado *StringSerializer*, deve-se usar o *StringDeserializer*, etc.

> **Poison pills** 
>
> Mensagens que não respeitam o acôrdo de formato de serialização.

### Consumer Groups
  
O consumidores dentro de um mesmo grupo são capazes de coordenar a separação do trabalho de leitura de diferentes partições. Qualquer *consumer* faz parte de um *consumer group*.
- cada *consumer* dentro de um grupo lê de partições exclusivas;
- se houver mais *consumers* que partições, esses *consumers* ficarão inativos;

![Consumer group](/img/kafka-consumer-group01.png)


Múltiplos *consumer groups* podem consumir de um mesmo tópico ao mesmo tempo. Cada *consumer group* possui um ***group.id***. 

![Multiplos consumer groups](/img/kafka-consumer-group02.png)

### Consumer Offsets

Os *brokers* Kafka usam um tópico interno (*__consumer_offsets*) que rastreia as mensagens que um determinado grupo de consumidores processou com sucesso pela última vez.

Regularmente, o consumidor executa o *commit* da última mensagem processada, incrementando o *offset* e gerando um *consumer offset*, funciona como um *chekpoint*.

A maioria das implementações (*libs*) executa o *commit* de forma automática de tempos em tempos (período) e o *broker* escreve no tópico *__consumer_offsets* (Não é o consumer que escreve nesse tópico!!). 

#### Pontos importantes:
- Se algum cliente (*consumer*) tem um problema e pára ou "quebra", ocorre um *rebalance* e o último *offset* comitado indica onde os *consumers* devem reiniciar o processo de leitura;
- Se novos *consumers* são adicionados ao grupo, ocorre um *rebalance* e *offset* indica onde iniciar o processamento.

Por default, consumidores *Java* executam o *commit* automático:
```yaml
enable.auto.commit=true
```
Ocorre a cada 5s:
``` yaml
auto.commit.interval.ms=5000
```
quando o método ***.poll()*** é chamado.

O consumidor pode escolher quando comitar o *offset*:
```yaml
enable.auto.commit=false
```

### Delivery Semantics

Um *consumer* pode escolher quando executar os *commit offsets*.  Na reinicialização dos *consumers* as mensagens poderão ser ignoradas ou lidas 2 vezes, dependendo da estratéiga escolhida.

Existem 3 estratégias de *delivery semantics* :
- At most once (no máximo uma vez)  
Neste caso, as mensagesn são comitadas assim que o *lote* (batch) de mensagens é recebido após a chamada do *poll()*. Se o processamento falhar, as mensagens serão perdidas, pois a próxima leitura (chamada de poll()) trará dados novos.  
Pode ser adequado para sistemas que suportam perda de dados;
- At least once (pelo menos uma vez)  
Neste caso, as mensagens são comitadas após o processamento. Pode causar duplicidade de mensagens.  Se o processamento der erro, a mensagem poderá ser lida novamente.  
Essa é a estratégia mais usada!!
> Idempotência
> Tratamento para que, no caso de uma mesma mensagem seja processada novamente, não cause impacto no sistema.
- Exactly once (Exatamente uma vez)  
Cada mensagem é entregue apenas uma vez.   
Aqui existem 2 possibilidades de alcançar este modelo:  
1. Usando API Transasional do Kafka, de tópico para tópico.  *Kafka Streams* simplifica isso através do uso da propriedade: ```processing.guarantee=exactly.once```
2. No caso de mensagens tópico para fora, a única maneira é tornar o *consumer* idempotente.

### Estratégia de *commit* automático de *offset*

Por padrão, a API Java do Kafka Consumer comita os *offsets* regularmente e automaticamente, implicando na estratégia *at least once*. 
```enable.auto.commit=true``` faz com que os *offsets* sejam comitados automaticamente com uma frequência controlada por ```auto.commit.interval.ms=5000```.
1. o método *poll()* é chamado;
2. o tempo entre 2 chamadas de *poll()*  for maior que ```auto.commit.interval.ms```


### Kafka Broker

Um *broker* é um servidor Kafka. Cada broker é identificado por um id numérico único. Um cluster é formado por um conjunto de brokers.

O broker armazena os dados em um diretório no disco do servidor. Cada tópico possui um sub-diretório com o nome do tópico.  Para ter escalabilidade e balancemento, os tópicos são dividios em partições e estas são distribuídas entre os brokers do cluster.

Cada broker no cluster possui metadados sobre os demais brokers. Assim, basta que um cliente se conecte à um broker para conhecer os demais. 

![Bootstrap Server](/img/kafka-client-brokers.png)

É uma boa prática o cliente se referir a pelo menos 2 brokers na url de conexão.

### Kafka Topic Replication

#### Replication Factor

Replicação de dados previne perda escrevendo o mesmo dado em mais de um broker.

![Replication](/img/kafka-replication.png)


Cada partição tem um líder (broker leader)  e múltiplas réplicas (se replication factor for maior que 1)

### Configuração do Producer *acks*

Um *kafka producer* pode especificar o nível de *acks* para dizer se a mensagem  precisa ser escrita em um número mínimo de réplicas antes de ser considerado sucesso!

Por padrão:
- se Kafka < v3.0; acks=1
- se Kafka > v3.0; acks=all

#### acks=0

Considera **SUCESSO** no momento que a mensagem foi enviada., sem aguardar retorno do broker.
Se o broker estiver *offline* ou ocorrer alguma *exception* o *producer* não saberá e o dado será perdido.
Usado quando uma possível perda de mensagem é aceitável: coleta de métricas, por exemplo.

#### acks=1

Considera **SUCESSO**  quando um *broker leader* retorna ok.  
A replicação não é garantida.

#### acks=all

Considera **SUCESSO** quando a mensagem é aceita por todos os *in-sync replicas* (ISR).  
O *leader* verifica se há o número mínimo de réplicas (min.insync.replicas) para retornar sucesso.  
A propriedade pode ser configurada tanto à nível de tópico, quanto de broker.
A quantidade configurada em min.insync.replica considera o *leader* na contagem.  
Se não houverem replicas suficientes respondendo (not available), o *producer* (cliente) receberá uma exeception:  *NotEnoughReplicasException*

### Topic Durability & Availability

>acks=all  
>min.insync.replicas=M  
>replication.factor=N  
>N-M brokers podem estar indisponíveis e o tópico ainda será considerado disponível.


Kafka consumers, por padrão, leem da partiação líder.



