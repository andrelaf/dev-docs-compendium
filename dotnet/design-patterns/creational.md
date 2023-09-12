## Criacionais
  
1. ### Singleton

**Descrição:** Garante que uma classe tenha apenas uma instância e fornece um ponto de acesso global a ela.

**Intenção:** Usar quando quiser garantir que uma classe seja instanciada apenas uma vez, como para um gerenciador de configurações.

**Exemplo Prático:**

```csharp
//Example 1:
public sealed class BankConfiguration
{
    private static BankConfiguration _instance;
    private static readonly object _lock = new object();

    public string BankName { get; private set; }

    private BankConfiguration()
    {
        BankName = "OpenBank";
    }

    public static BankConfiguration Instance
    {
        get
        {
            lock (_lock)
            {
                return _instance ??= new BankConfiguration();
            }
        }
    }
}

//Usage:
var config = BankConfiguration.Instance;
Console.WriteLine($"Bank Name: {config.BankName}");

// Example 2: 
public sealed class ConfigurationManager
{
    private static readonly Lazy<ConfigurationManager> instance = new Lazy<ConfigurationManager>(() => new ConfigurationManager());

    private ConfigurationManager() {}

    public static ConfigurationManager Instance => instance.Value;

    public string GetSetting(string key)
    {
        // Logic to get setting by key
        return "SomeSettingValue";
    }
}

//Example 3:
public sealed class ConfigurationManager
{
    private static ConfigurationManager? instance;
    private static readonly object padlock = new();

    private ConfigurationManager() { }

    public static ConfigurationManager Instance
    {
        get
        {
            if (instance is null)
            {
                lock (padlock)
                {
                    instance ??= new ConfigurationManager();
                }
            }
            return instance;
        }
    }

    public string GetSetting()
    {
        // Logic to get setting by key
        return "SomeSettingValue";
    }
}

//Usage:
 var settingValue = ConfigurationManager.Instance.GetSetting("SomeKey");

```

2. ### Factory Method

**Descrição:** Define uma interface para criar objetos, mas permite que as subclasses decidam que classe instanciar.

**Intenção:** Ideal quando uma classe não pode antecipar a classe dos objetos que deve criar.

**Exemplo Prático:**

```csharp
//Example 1:
public interface IAccount
{
    void DisplayAccountType();
}

public class SavingsAccount : IAccount
{
    public void DisplayAccountType()
    {
        Console.WriteLine("Savings Account");
    }
}

public class CheckingAccount : IAccount
{
    public void DisplayAccountType()
    {
        Console.WriteLine("Checking Account");
    }
}

public abstract class AccountFactory
{
    public abstract IAccount CreateAccount();
}

public class SavingsAccountFactory : AccountFactory
{
    public override IAccount CreateAccount() => new SavingsAccount();
}

public class CheckingAccountFactory : AccountFactory
{
    public override IAccount CreateAccount() => new CheckingAccount();
}

//Usage:
AccountFactory factory = new SavingsAccountFactory();
IAccount account = factory.CreateAccount();
account.DisplayAccountType();

//Example 2: 
public abstract class UserCreator
{
    public abstract User CreateUser();
}

public class AdminCreator : UserCreator
{
    public override User CreateUser()
    {
        return new AdminUser();
    }
} 
```

3. ### Abstract Factory

**Descrição:** Fornece uma interface para criar famílias de objetos relacionados sem especificar suas classes concretas.

**Intenção:** Usar quando sistemas precisam criar produtos de múltiplas famílias.

**Exemplo Prático:**
```csharp

//Example 1:
var account = new BankAccount.Builder()
    .WithOwner("John Doe")
    .WithInitialBalance(1000.0m)
    .Build();

Console.WriteLine($"Owner: {account.Owner}, Balance: {account.Balance}");
//Usage:
AbstractFactory factory = new SavingsGoldFactory();

IAccount account = factory.CreateAccount();
account.Deposit(2000);
account.DisplayAccountType();

ICreditCard card = factory.IssueCard();
card.Charge(500);
card.DisplayCardType();

//Example 2: 
public interface IUserFactory
{
    User CreateUser();
}

public class AdminFactory : IUserFactory
{
    public User CreateUser()
    {
        return new AdminUser();
    }
}
```

4. ### Builder

**Descrição:** Separa a construção de um objeto complexo de sua representação.

**Intenção:** Ideal quando um objeto precisa ser criado com muitas partes opcionais.

**Exemplo Prático:**
```csharp
//Example 1: 
public class BankAccount
{
    public string Owner { get; set; }
    public decimal Balance { get; set; }
    // Other properties

    public class Builder
    {
        private BankAccount _account = new BankAccount();

        public Builder WithOwner(string owner)
        {
            _account.Owner = owner;
            return this;
        }

        public Builder WithInitialBalance(decimal balance)
        {
            _account.Balance = balance;
            return this;
        }

        public BankAccount Build()
        {
            return _account;
        }
    }
}

//Usage:
var account = new BankAccount.Builder()
    .WithOwner("John Doe")
    .WithInitialBalance(1000.0m)
    .Build();

Console.WriteLine($"Owner: {account.Owner}, Balance: {account.Balance}");
//Example 2:
public class UserBuilder
{
    private string _name;

    public UserBuilder SetName(string name)
    {
        _name = name;
        return this;
    }

    public User Build()
    {
        return new User { Name = _name };
    }
}
```

5. ### Prototype

**Descrição:** Cria objetos clonando um protótipo existente.

**Intenção:** Usar quando a instânciação de uma classe é mais cara que clonar um objeto existente.

**Exemplo Prático:**
```csharp
//Example 1:
public interface IAccountPrototype
{
    IAccountPrototype Clone();
    decimal Balance { get; set; }
}

public class BankAccount : IAccountPrototype
{
    public decimal Balance { get; set; }

    public IAccountPrototype Clone()
    {
        return (IAccountPrototype) this.MemberwiseClone();
    }
}
//Usage:
BankAccount originalAccount = new BankAccount { Balance = 1000.50m };
BankAccount clonedAccount = (BankAccount)originalAccount.Clone();

Console.WriteLine($"Original Balance: {originalAccount.Balance}");
Console.WriteLine($"Cloned Balance: {clonedAccount.Balance}");

//Example 2: 
public interface IPrototype
{
    IPrototype Clone();
}

public class User : IPrototype
{
    public string Name { get; set; }

    public IPrototype Clone()
    {
        return this.MemberwiseClone() as IPrototype;
    }
}

// Uso:
var originalUser = new User { Name = "John" };
var clonedUser = originalUser.Clone();
```