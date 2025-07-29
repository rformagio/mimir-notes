## Kafka - CLI

### Kafka Topics

```sh
kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --create --partitions 3 --replication-factor 1
```


Pontos importantes:
- *replication factor* não pode ser maior que o número de *brokers*;
- pode-se especificar quantas partições quiser;
- SE, ao executar um comando, aparecer a doc, há algum erro no comando;
- o nome do tópico deve ser ASCII alfanumérico, ".", "_" e "-". Porém, nunca usar "_" e "." no mesmo nome !!

Configurar nível de tópico:

--config

Para listar os tópicos:
```sh
kafka-topics.sh --bootstrap-server localhost:9092 --list
```

Porém, este comando também lista tópicos internos, que não foram criados pelo usuário. PAra não listá-los, usar:

--exclude-internal

Para detalhar um tópico:
```sh
kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic first_topic
```

Exemplo de saída em um cluster:
```sh
Topic: first_topic	TopicId: 84yqCErzTG27J4wv44dkPQ	PartitionCount: 4	ReplicationFactor: 3	Configs: cleanup.policy=delete
Topic: first_topic	Partition: 0	Leader: 2	Replicas: 2,3,1	Isr: 2,3,1
Topic: first_topic	Partition: 1	Leader: 3	Replicas: 3,1,2	Isr: 3,1,2
Topic: first_topic	Partition: 2	Leader: 1	Replicas: 1,2,3	Isr: 1,2,3
Topic: first_topic	Partition: 3	Leader: 2	Replicas: 2,1,3	Isr: 2,1,3
```

- *Leader: 2* = para partição 0, o *broker* com id 2 é o líder;
- *Replicas: 2,3,1* = para partição 0, os *brokers* com id 2, 3 e 1 são réplicas;
- *Isr: 2,3,1* = para partição 0, os *brokers* com id 2, 3 e 1 são *in-sync replicas*.

Pode-se aumentar o número de partições de um tópico via CLI, mas isso não é recomendado, principalmente se houver algum tipo de ordenção baseado na *key*. 

Para deletar um tópico:
```sh
kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic first_topic
```

Para que o tópico seja deletado, a propriedade do broker:
```json
delete.topic.enable
```
 precisa estar habilitada. Do contrário, o tópico apenas será marcado para deleção.

 ### Kafla Producer

 ```sh
 kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic
 ```

 Vai aparecer um :
 ```sh
 >
 ```
 
 Digite  o que quiser enviar como mensagem. Para sair, digite *Ctrl + C*

 Por padrão as mensagens são enviadas com *key=null*

 Se o tópico não existir, ele poderá ser criado automaticamente dependendo as configuraçãos do *broker*.
 ```json
 auto.create.topics.enable=true
num.partitions=1
default.replication.factor=1
 ```

 Pode-se habilitar a compressão de mensagens:
 ```sh
 --compression-codec
 ```

 passando um dos valores: 'none', 'gzip', 'snappy', 'lz4', or 'zstd'.

 Para configurar alguma propriedade:
 ```sh
 --producer-property acks=all
 ```

 Também é possível enviar mensagens contidas em um arquivo, sendo que cada mesnagem deve estar em uma linha diferente:
 ```sh
 kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic < topic-input.txt
 ```

Para enviar mensagens com *keys* :
```sh
kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic --property parse.key=true --property key.separator=:
```
Exemplo:
```sh
> key:value
> name:John
> cpf:{"name":"John", "age":"20"}
```


### Kafka Consumer







