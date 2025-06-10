# Kafka - Um Pouco de Teoria

Apache Kafka é um sistema distribuido, de código aberto, composto por servidores e clientes, usado principalmente para construir pipelines de streaming de dados em tempo real.
É muito usado para:
- pipeline de dados de alta performance
- integração de dados
- streaming analytics

Integração de dados é um desafio. Empresas costumam ter muitas fontes de dados, cada qual com seus formatos diferentes. Integrar significa combinar e unificar os dados em uma visão única. Kafka permite desacoplar as fontes de streams dados dos consumidores alvos. 
Um flustream o de dados é normalmente considerado uma sequência de dados potencialmente ilimitada. O nome streaming é usado porque queremos que os dados sejam acessíveis assim que são produzidos.

### Não usar

Kafka não deve ser usado como uma ferramenta de filas. Kafka é formado por tópicos, não filas. Filas permitem que milhões de consumidores escalem e as mensagens são deletadas após consumidas. No Kafka os dados não são deletados após serem consumidos e os consumidores não escalam além da quantidade de partições no tópicos. 

Também não deve ser uado como uma base de dados com índices, pois o Kafka não permite queries complexas.

## Conceitos

### Tópicos

Tópicos são como tabelas de dados sem *constraints*.  Pode-se ter quantos tópicos quanto necessário e cada um é idenficiado por um nome. São imutáveis, cada vezque o dado é escrito em uma *partição* não podemais ser alterado.

#### Partições: tópicos são dividios em partições;
#### Offset: é um *id* incremental, por partição. Não pode ser reaproveitado. Só faz sentido na partição que foi criado.

***A ordem só é garantida dentro de uma mesma partição***

