# Kafka - Um Pouco de Teoria

Apache Kafka é um sistema distribuido, de código aberto, composto por servidores e clientes, usado principalmente para construir pipelines de streaming de dados em tempo real. É uma plataforma de fluxo de eventos, capas de manipular milhões de mensagens por segundo.

***Um stream o de dados é normalmente considerado uma sequência de dados potencialmente ilimitada. O nome streaming é usado porque queremos que os dados sejam acessíveis assim que são produzidos.***

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



