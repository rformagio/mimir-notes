# MongoDB com Python (usando pymongo)

## Instalação
```bash
pip install pymongo
```

## Conexão com o MongoDB

```python
from pymongo import MongoClient

# Conecta ao MongoDB local
client = MongoClient("mongodb://localhost:27017/")

# Acessa o banco e a coleção
db = client["empresa"]
colecao = db["funcionarios"]
```

Para usar MongoDB Atlas:

```python
client = MongoClient("mongodb+srv://usuario:senha@cluster.mongodb.net/empresa")
```

