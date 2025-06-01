# OOP - Object Oriented Programing

É uma forma de estruturar um programa adicionando propriedades e comportamentos em objetos.

Na OOP, cria-se classes que tentam representar (abstrair) objetos, situações, operações, pessoas, etc ...e seus comportamentos. 

## Alguns conceitos

Classes: São tipos de dados definidos pelos programadores

Objetos: São instâncias das classes

Metodos: São funções que os objetos podem executar

Atributos: São características dos objetos. Representam seus estados.

## Pilares

### Abstração
Pode ser definida como o processo de identificar apenas as características principais e obrigatórias dos objetos, ignorando detalhes irrelevantes.
É a representação de coisas/objetos complexos através de Classes.

### Encapsulamento
Pode ser definido como o processo de "esconder" ou "encobrir" os dados dentro das classes. Ou seja, os dados do objeto ficam escondidos, protegidos, são privados e só podem ser acessados através de métodos públicos

### Herança
É um mecanismo pelo qual uma classe pode herdar propriedades e métodos de outra classe. Também conhecido como relação "É-UM"
Aqui entram os conceitos de SuperClasse e SubClasse. E também "reusabilidade".
A herança permite criar novas classes compartilhando atributos de classes já existentes. Ou seja, permite usar a mesma "palavra" para represntar coisas diferentes em contextos diferentes.

### Polimorfismo
É a habilidade de diferenciar entre duas classes com mesmo nome!

Existem dois tipos de polimorfismo:

- Méthod Overloading (Polimorfismo Estático)
Implementar 2 ou mais métodos com mesmo nome, mas número de argumentos diferentes; OU
Implementar 2 ou mais métodos com mesmo nome, mas com argumentos de tipos diferentes
Não é possível ter 2 métodos que diferem apenas pelo retorno !!
Type Promotion (Widening Primitive) : Um tipo é implicitamente promovido a outro quando não combina com os tipos de argumento passados..
Pode ocorrer com métodos private e final

- Method Overriding (Polimorfismo Dinâmico) 
Uso de herança (@Override) - OOP -> LSP
Se o método da classe base retorna void ->  método overridden deve retornar void
Se o método da classe base retorna primitivo->  método overridden deve retornar primitivo
Se o método da classe base retorna 1 determinado tipo->  método overridden deve retornar o mesmo tipo ou um subtipo (covariant return type)
Se o método da classe base dispara uma exception ->  método overridden deve disparar a mesma exception ou subtipo
Não podem ocorrer em métodos private e final




## SOLID



## Design Patterns

