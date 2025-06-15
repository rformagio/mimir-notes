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
    - Implementar 2 ou mais métodos com mesmo nome, mas número de argumentos diferentes; OU
    - Implementar 2 ou mais métodos com mesmo nome, mas com argumentos de tipos diferentes
    - Não é possível ter 2 métodos que diferem apenas pelo retorno !!
    - Type Promotion (Widening Primitive) : Um tipo é implicitamente promovido a outro quando não combina com os tipos de argumento passados..
    - Pode ocorrer com métodos private e final

- Method Overriding (Polimorfismo Dinâmico) 
Uso de herança (@Override) - OOP -> LSP
    -  Se o método da classe base retorna void ->  método overridden deve retornar void
    - Se o método da classe base retorna primitivo->  método overridden deve retornar primitivo
    - Se o método da classe base retorna 1 determinado tipo->  método overridden deve retornar o mesmo tipo ou um subtipo (covariant return type)
    - Se o método da classe base dispara uma exception ->  método overridden deve disparar a mesma exception ou subtipo
    - Não podem ocorrer em métodos private e final




## SOLID

- SRP: Single Responsability Principle
    Criar classes com responsabilidades únicas

- OCP: Open Closed Principle
    Extender sem alterar código

- LSP: Liskov Substitution Principle

- ISP: InterfaceSgregation Principle


- DIP: Dependency Inversion Principle 

## Composição vs Herança

### Herança:

A herança estabelece uma relação "é um" entre classes. Uma classe filha (subclasse) herda atributos e métodos de uma classe pai (superclasse). Isso significa que a subclasse é um tipo mais específico da superclasse.

#### Vantagens
- Reuso de código
- Hierarquia natural;: modela relações claras, como "Um carro é um veículo"
#### Desvantagens
- Acoplamento forte
- Complexidade em linguagnes que suportam herança múltipla
- Hierarquia rígida: difícil alterar estrutura conforme requisitos mudam

```java
class Veiculo {
    int velocidade;

    void acelerar() {
        // Lógica para acelerar
    }
}

class Carro extends Veiculo {
    int numeroPortas;
}
```

### Composição:

A composição estabelece uma relação "tem um", onde uma classe contém uma instância de outra classe como um de seus atributos. Em vez de herdar comportamentos, a classe delega a responsabilidade para o objeto que ela "tem".

#### Vantagens
- Baixo acoplamento: classes menos dependentes, facilita manutenção
- Reuso de código flexível: reusa comportamentos sem se prender a uma hierarquia
- Melhora coesão: classes se concentram em suas prórprias responsabilidades
#### Desvantagens
- Pode aumentar o número de objetos: várias classes menores para obter um conjunto de comportamentos

```java
class Motor {
    void ligar() {
        // Lógica para ligar o motor
    }
}

class Carro {
    Motor meuMotor; // Composição: Um Carro tem um Motor

    Carro() {
        this.meuMotor = new Motor();
    }

    void ligarCarro() {
        this.meuMotor.ligar(); // Delegação
    }
}
```

## Design Patterns

1. Factory Method

Aqui não podemos pecar por uma definição simplória, de que basta um método criar um objeto que está aplicando o pattern

Para  ser considerado *Factory Method* devem-se cumprir 3 requisitos:
- Criar um novo objeto;
- O retorno deve ser uma interface ou classe abastrata;
- É necessário existirem vários objetos que implementam ou extendem a interface/classe

No *Factory Method*, o usuário não tem ideia de qual é a classe retornada pelo método. Ele conhece apenas a interface. A decisão de qual classe retornar é do método.

2. Abstract Factory

3. Strategy

4. Singleton

5. Command