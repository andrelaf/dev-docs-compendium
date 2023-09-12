# Generics

## Classe Genéricas:

**Quando usar:** Quando você tem uma classe que fornece a mesma implementação, independentemente do tipo de dado que ela armazena.

**Exemplo:**
```csharp
public class Caixa<T>
{
    private T item;

    public void Guardar(T item)
    {
        this.item = item;
    }

    public T Pegar()
    {
        return item;
    }
}

// Usage:
Caixa<string> caixaDeString = new Caixa<string>();
caixaDeString.Guardar("Olá, mundo!");

Caixa<int> caixaDeInt = new Caixa<int>();
caixaDeInt.Guardar(123);

```
## Métodos Genéricos:

**Quando usar:** Quando a lógica do método é a mesma, independentemente do tipo de dado com o qual ele opera.

**Exemplo:** 
```csharp
public void ExibirDados<T>(T dado)
{
    Console.WriteLine($"O dado é: {dado}");
}

// Usage:
ExibirDados<string>("Olá, mundo!");
ExibirDados<int>(123);

```
## Interfaces Genéricas:
**Quando usar:** Quando você quer garantir que várias classes tenham um conjunto definido de métodos que operam em um tipo específico, mas esse tipo só será definido no momento da implementação.

**Exemplo:** 
```csharp
public interface IArmazenador<T>
{
    void Guardar(T item);
    T Pegar();
}
public class CaixaString : IArmazenador<string>
{
    private string item;
    
    public void Guardar(string item)
    {
        this.item = item;
    }

    public string Pegar()
    {
        return item;
    }
}

```
