# 🧠 MongoDB – Guia Completo: Do Básico ao Avançado

---

## 📌 O que é MongoDB?

MongoDB é um banco de dados NoSQL orientado a documentos. Ele armazena dados em formato **JSON binário (BSON)**, sendo altamente escalável, flexível e ideal para aplicações modernas que demandam alta performance.

- **Modelo de dados flexível** (schema-less)
- **Alta performance** para leitura/escrita
- **Alta disponibilidade** com replica sets
- **Escalabilidade horizontal** com sharding
- **Indexação poderosa**, incluindo geoespacial e full-text

---

## 🔍 Conceitos Básicos

| Conceito     | Descrição |
|--------------|-----------|
| **Database** | Conjunto de coleções |
| **Collection** | Similar a uma tabela relacional |
| **Document** | Objeto JSON armazenado em BSON |
| **Field**     | Atributo de um documento |

---

## Estrutura de Dados

MongoDB não usa tabelas e linhas como em bancos relacionais, mas sim:

```scss
Banco de Dados
└── Coleção (Collection)
    └── Documento (Document)
```

- Documentos: unidades de dados armazenadas em formato JSON-like (BSON).

- Coleções: agrupamento de documentos (sem schema fixo).

## Exemplo de Documento

```json
{
  "_id": ObjectId("66a73f1c0a5e4fcbe6"),
  "nome": "Rodrigo",
  "idade": 35,
  "habilidades": ["Java", "AWS", "MongoDB"],
  "endereco": {
    "cidade": "São Paulo",
    "estado": "SP"
  }
}
```


## Operações Básicas

### Create
```javascript
db.usuarios.insertOne({ nome: "Rodrigo", idade: 35 })
```

### Read
```javascript
db.usuarios.find({ idade: { $gte: 30 } })
```

### Update
```javascript
db.usuarios.updateOne(
  { nome: "Rodrigo" },
  { $set: { idade: 36 } }
)
```
### Delete
```javascript
db.usuarios.deleteOne({ nome: "Rodrigo" })
```

## Operadores e Consultas

### Operadores de Comparação

- $eq, $ne, $gt, $gte, $lt, $lte, $in, $nin
```javascript
db.usuarios.find({ idade: { $gte: 30, $lte: 40 } })
```

### Operadores Lógicos

- $and, $or, $not, $nor
```javascript
db.usuarios.find({ $or: [ { idade: { $lt: 25 } }, { idade: { $gt: 60 } } ] })
```

### Busca por campo aninhado
```javascript
db.usuarios.find({ "endereco.cidade": "São Paulo" })
```

## Índices

MongoDB cria um índice padrão no campo _id, mas você pode criar outros para melhorar a performance:

```javascript
db.usuarios.createIndex({ nome: 1 })         // índice crescente
db.usuarios.createIndex({ idade: -1 })       // índice decrescente
```

##  Aggregation Framework (Consultas avançadas)

Permite agregações complexas semelhantes a GROUP BY e JOIN.

Exemplo: média de idade por cidade
```javascript
db.usuarios.aggregate([
  { $group: { _id: "$endereco.cidade", media_idade: { $avg: "$idade" } } }
])
```

## Modelagem de Dados (Embedded vs Referenciado)

- Embedded (aninhado): rápido para leitura, ótimo quando os dados são usados juntos.
- Referenciado: útil quando os dados crescem muito ou são usados separadamente.


Embedded:
```json
{
  "pedido_id": 123,
  "itens": [
    { "produto": "Notebook", "quantidade": 1 },
    { "produto": "Mouse", "quantidade": 2 }
  ]
}
```

Referenciado (normalizado):
```json
{
  "pedido_id": 123,
  "cliente_id": ObjectId("...")
}
```

## Replicação e Sharding (conceitos avançados)

- Replica Set: Alta disponibilidade. Um conjunto de instâncias com um primário e réplicas secundárias.

- Sharding: Distribuição horizontal de dados em múltiplos servidores. Usado para escalar bancos de dados com muitos dados.

## Segurança

- Autenticação e autorização com roles (RBAC)
- Conexão segura com TLS/SSL
- Criptografia em repouso (MongoDB Enterprise)

## MongoDB + Java + Spring Boot

```java
@Document(collection = "usuarios")
public class Usuario {
  @Id
  private String id;
  private String nome;
  private int idade;
}

public interface UsuarioRepository extends MongoRepository<Usuario, String> {
  List<Usuario> findByIdadeGreaterThan(int idade);
}
```

## Ferramentas úteis

| Ferramenta        | Finalidade                          |
| ----------------- | ----------------------------------- |
| **MongoDB Atlas** | MongoDB na nuvem (plano grátis)     |
| **Compass**       | GUI para explorar bancos            |
| **mongosh**       | Shell interativo moderno            |
| **Mongoose**      | ODM para Node.js (útil p/ comparar) |




## ⚙️ Operações CRUD com MongoDB em Python

### 📦 Instale o driver

```bash
pip install pymongo
```

### 🔌 Conexão simples

```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017/")
db = client["empresa"]
colecao = db["funcionarios"]
```

---

### 📝 Inserção de documentos

```python
# Único documento
colecao.insert_one({"nome": "Alice", "cargo": "Engenheira", "salario": 12000})

# Vários documentos
colecao.insert_many([
    {"nome": "Bob", "cargo": "DevOps"},
    {"nome": "Carla", "cargo": "Analista"}
])
```

---

### 🔍 Consultas (queries)

```python
# Todos os documentos
for doc in colecao.find():
    print(doc)

# Filtro com projeção
for doc in colecao.find({"cargo": "Engenheira"}, {"_id": 0, "nome": 1}):
    print(doc)
```

---

### ✏️ Atualizações

```python
colecao.update_one({"nome": "Alice"}, {"$set": {"salario": 14000}})
```

---

### ❌ Remoções

```python
colecao.delete_one({"nome": "Bob"})
```

---

### 🚀 Inserção em batch com performance

```python
from pymongo import InsertOne

lote = [InsertOne({"i": i}) for i in range(100000)]
colecao.bulk_write(lote)
```

---

### 📈 Indexação

```python
colecao.create_index("nome")  # Índice simples
colecao.create_index([("nome", 1), ("idade", -1)])  # Índice composto
```

---

## 🧪 Testando Replica Set com Docker

Você pode rodar um cluster MongoDB com 3 réplicas usando Docker Compose. Isso simula um ambiente de produção com failover.

---

### 📁 Estrutura esperada

```
mongo-cluster/
├── docker-compose.yml
└── replica-init.js
```

---

### 📄 docker-compose.yml

```yaml
version: "3.8"

services:
  mongo1:
    image: mongo:7
    container_name: mongo1
    ports:
      - "27017:27017"
    volumes:
      - mongo1_data:/data/db
      - ./replica-init.js:/docker-entrypoint-initdb.d/replica-init.js
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=admin
    command: mongod --replSet rs0

  mongo2:
    image: mongo:7
    container_name: mongo2
    ports:
      - "27018:27017"
    volumes:
      - mongo2_data:/data/db
    command: mongod --replSet rs0

  mongo3:
    image: mongo:7
    container_name: mongo3
    ports:
      - "27019:27017"
    volumes:
      - mongo3_data:/data/db
    command: mongod --replSet rs0

volumes:
  mongo1_data:
  mongo2_data:
  mongo3_data:
```

---

### 📄 replica-init.js

```javascript
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo1:27017" },
    { _id: 1, host: "mongo2:27017" },
    { _id: 2, host: "mongo3:27017" }
  ]
})
```

---

### 🚀 Como executar

```bash
docker-compose up -d
docker exec -it mongo1 mongosh
rs.status()
```

---

### 🔗 Conectando com Python

```python
client = MongoClient("mongodb://localhost:27017,localhost:27018,localhost:27019/?replicaSet=rs0")
```

---

## 🧰 Ferramentas úteis

- **MongoDB Compass** – GUI para visualizar e consultar
- **Atlas** – Plataforma em nuvem da MongoDB
- **mongosh** – Novo shell interativo
- **mongomock** – Testes unitários com Mongo em memória

---

## ✅ Próximos passos

- Estude agregações (`$group`, `$match`, `$project`)
- Aprenda sobre sharding
- Explore o Mongoose (ODM para Node.js)
- Use transactions com replica sets

