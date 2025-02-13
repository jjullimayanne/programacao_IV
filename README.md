.net 
Fluxo de execução
C#, compilador Roslyn que transforma o código em CIL, commom intermediate language. um bytecode que é entendido pela CLR que ai sim traduz o CIL para a linguagem de maquina. A CLR conta com garbage collectionm segurança, paralelismo.
# Fluxo de Compilação do C# no .NET

Este diagrama mostra como o código C# é transformado em código executável no .NET, passando pelo compilador Roslyn e podendo ser executado via **JIT** ou **AOT**.



```mermaid
graph TD;
    A[Código CSharp] -->|Compilacao Roslyn| B[Código Intermediario CIL ou MSIL];
    
    B -->|Execucao com JIT| C[Codigo Nativo em Tempo de Execucao];
    B -->|Execucao com AOT| D[Codigo Nativo Pre-Compilado];
    
    C -->|Rodando no CLR| E[Programa Executando];
    D -->|Executavel Independente| E;
    
    subgraph Caminho Tradicional JIT
        B --> C;
        C --> E;
    end
    
    subgraph Caminho AOT Opcional
        B --> D;
        D --> E;
    end


```

# Estrutura da CLR (.NET Runtime)

A **Common Language Runtime (CLR)** é a máquina virtual responsável por executar aplicações .NET, gerenciando memória, segurança e otimizações de desempenho.

```mermaid
graph TD;
    A[Código CSharp, FSharp, VB.NET] -->|Compilação Roslyn| B[Código Intermediário CIL ou MSIL];
    
    B -->|Carregado pelo CLR| C[Common Language Runtime];
    
    subgraph CLR
        C1[Gerenciamento de Memória] 
        C2[Compilação JIT] 
        C3[Garbage Collector] 
        C4[Segurança e Verificação] 
        C5[Execução do Código]
    end
    
    C --> C1;
    C --> C2;
    C --> C3;
    C --> C4;
    C --> C5;
    
    C5 -->|Código Nativo| D[Execução no Sistema Operacional];

```

Tipos
Todos os tipos por valor são structs: Isso inclui os tipos primitivos como int, float, double, bool, char, além de estruturas (struct) definidas pelo usuário.
Eles herdam de `System.ValueType`: Isso ocorre implicitamente, ou seja, quando você define uma struct, internamente ela já está herdando de System.ValueType, que por sua vez deriva de System.Object.

```c#
int numero = 10; // int é um Value Type, que é um struct
Console.WriteLine(numero.GetType()); // Saída: System.Int32


```

```
System.Object
   └── System.ValueType
         ├── System.Int32 (int)
         ├── System.Double (double)
         ├── System.Boolean (bool)
         ├── System.Char (char)
         ├── System.DateTime
         └── MinhasStructsPersonalizadas


```

No .NET, System.Object (ou apenas object) é a classe base de todos os tipos. Isso significa que todas as classes e tipos de referência derivam, direta ou indiretamente, dela.

Diferente dos Value Types (tipos por valor, como int, struct, bool), os Reference Types (objetos, class, string, array) são armazenados no heap e acessados por referência.

```mermaid
graph TD;
    subgraph Stack Memory
        mainFunction["main()"]
        p1["p1 (reference) ➝ obj1"]
        p2["p2 (reference) ➝ obj1"]
        x["x = 10 (Value Type)"]
        y["y = x (copy of x)"]
    end

    subgraph Heap Memory
        obj1["obj1 { Nome: 'Carlos' }"]
    end

    p1 -->|Reference| obj1
    p2 -->|Reference| obj1
    y -->|Copy of x| x



```
O que acontece quando usamos new?
Quando você cria um objeto com new, o .NET faz o seguinte:

- Aloca um espaço na memória heap para armazenar o objeto.
- Inicializa os valores dos membros (com valores padrão se não forem especificados).
- Retorna uma referência para o objeto criado na heap.
- Essa referência é armazenada na stack, para que o programa possa acessar o objeto.

```c#
class Pessoa
{
    public string Nome;
}

Pessoa p1 = new Pessoa(); // Aloca um novo objeto no heap
p1.Nome = "Carlos";

Pessoa p2 = new Pessoa(); // Aloca um NOVO objeto no heap
p2.Nome = "Ana";

Console.WriteLine(p1.Nome); // Carlos
Console.WriteLine(p2.Nome); // Ana


```
