### SOLID

SOLID é um acrônimo que representa cinco princípios fundamentais para escrever software orientado a objetos de qualidade:

- **S** - **Single Responsibility Principle (SRP)**: Uma classe deve ter apenas uma razão para mudar, o que significa que deve ter apenas uma responsabilidade.
  
**Violação:**
A classe ContaBancaria está fazendo mais do que deveria. Ela gerencia informações da conta, realiza operações e ainda gera relatórios.

```csharp
public class ContaBancaria
{
    public int Id { get; set; }
    public string Titular { get; set; }
    public double Saldo { get; private set; }

    public void Depositar(double valor)
    {
        Saldo += valor;
    }

    public void Sacar(double valor)
    {
        if (valor <= Saldo)
        {
            Saldo -= valor;
        }
        else
        {
            throw new Exception("Saldo insuficiente.");
        }
    }

    public string GerarRelatorio()
    {
        return $"Conta: {Id}, Titular: {Titular}, Saldo: {Saldo}";
    }
}
```
**Solução:**
Separe as responsabilidades em classes diferentes. Podemos ter uma classe OperacoesBancarias para lidar com saques e depósitos e outra classe RelatorioConta para gerar relatórios.

```csharp
public class ContaBancaria
{
    public int Id { get; set; }
    public string Titular { get; set; }
    public double Saldo { get; private set; }
}

public class OperacoesBancarias
{
    public void Depositar(ContaBancaria conta, double valor)
    {
        conta.Saldo += valor;
    }

    public void Sacar(ContaBancaria conta, double valor)
    {
        if (valor <= conta.Saldo)
        {
            conta.Saldo -= valor;
        }
        else
        {
            throw new Exception("Saldo insuficiente.");
        }
    }
}

public class RelatorioConta
{
    public string Gerar(ContaBancaria conta)
    {
        return $"Conta: {conta.Id}, Titular: {conta.Titular}, Saldo: {conta.Saldo}";
    }
}
```
- **O** - **Open/Closed Principle (OCP)**: Software deve ser aberto para extensão, mas fechado para modificação.

**Violação:**
Considere uma classe Retangulo com propriedades de largura e altura. Se tivermos uma subclasse Quadrado, a relação pode quebrar, pois a altura e a largura de um quadrado são sempre iguais.
```csharp
public class Retangulo
{
    public virtual int Largura { get; set; }
    public virtual int Altura { get; set; }
}

public class Quadrado : Retangulo
{
    public override int Largura
    {
        set { base.Largura = base.Altura = value; }
    }

    public override int Altura
    {
        set { base.Altura = base.Largura = value; }
    }
}
```
**Solução:**
Evite a herança nesse caso. Em vez disso, use composição ou interfaces.
```csharp
public interface IForma
{
    int Area();
}

public class Retangulo : IForma
{
    public int Largura { get; set; }
    public int Altura { get; set; }

    public int Area()
    {
        return Largura * Altura;
    }
}

public class Quadrado : IForma
{
    public int Lado { get; set; }

    public int Area()
    {
        return Lado * Lado;
    }
}
```
- **L** - **Liskov Substitution Principle (LSP)**: Subtipos devem ser substituíveis por seus tipos base.
- 
**Violação:**
Uma interface IJogador que tem funções que nem todos os jogadores precisam.
```csharp
public interface IJogador
{
    void Correr();
    void Atirar();
    void Nadar();
}

public class JogadorTerrestre : IJogador
{
    public void Correr() { /* Correr */ }

    public void Atirar() { /* Atirar */ }

    public void Nadar() { /* Não faz sentido para jogadores terrestres! */ }
}
```
**Solução:**
Separe a interface em interfaces menores.
```csharp
public interface ICorredor
{
    void Correr();
}

public interface IAtirador
{
    void Atirar();
}

public interface INadador
{
    void Nadar();
}

public class JogadorTerrestre : ICorredor, IAtirador
{
    public void Correr() { /* Correr */ }

    public void Atirar() { /* Atirar */ }
}
```
- **I** - **Interface Segregation Principle (ISP)**: Clientes não devem ser forçados a depender de interfaces que não usam.

**Violação:**
Dependência direta de uma implementação de armazenamento de baixo nível.
```csharp
public class SistemaArquivos
{
    public void Salvar(string dados) { /* Salvar nos arquivos */ }
}

public class Negocio
{
    private SistemaArquivos _sistemaArquivos = new SistemaArquivos();

    public void ProcessarDados(string dados)
    {
        // Processar dados
        _sistemaArquivos.Salvar(dados);
    }
}
```
**Solução:**
Depender de uma abstração em vez da implementação concreta.
```csharp
public interface IArmazenamento
{
    void Salvar(string dados);
}

public class SistemaArquivos : IArmazenamento
{
    public void Salvar(string dados) { /* Salvar nos arquivos */ }
}

public class Negocio
{
    private IArmazenamento _armazenamento;

    public Negocio(IArmazenamento armazenamento)
    {
        _armazenamento = armazenamento;
    }

    public void ProcessarDados(string dados)
    {
        // Processar dados
        _armazenamento.Salvar(dados);
    }
}
```
- **D** - **Dependency Inversion Principle (DIP)**: Dependa de abstrações, não de concretizações.

