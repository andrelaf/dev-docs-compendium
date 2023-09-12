
## Estruturais

1. ### Adapter

**Descrição:** Usar quando a instânciação de uma classe é mais cara que clonar um objeto existente.

**Intenção:** Permite que classes com interfaces incompatíveis trabalhem juntas.

**Exemplo Prático:**
```csharp
// Existing interface
public interface IOldSystemBankAccount
{
    void ProcessOldAccount();
}

public class OldSystemBankAccount : IOldSystemBankAccount
{
    public void ProcessOldAccount()
    {
        Console.WriteLine("Processing old system bank account...");
    }
}

// New required interface
public interface IBankAccount
{
    void ProcessAccount();
}

public class AccountAdapter : IBankAccount
{
    private readonly IOldSystemBankAccount _oldAccount;

    public AccountAdapter(IOldSystemBankAccount oldAccount)
    {
        _oldAccount = oldAccount;
    }

    public void ProcessAccount()
    {
        _oldAccount.ProcessOldAccount();
    }
}

//Usage:
IOldSystemBankAccount oldAccount = new OldSystemBankAccount();
IBankAccount account = new AccountAdapter(oldAccount);
account.ProcessAccount();
```
2. ### Bridge

**Descrição:** Permite que classes com interfaces incompatíveis trabalhem juntas.

**Intenção:** Usar quando queremos evitar um vínculo permanente entre uma abstração e sua implementação.

**Exemplo Prático:**
```csharp
interface IBank
{
    void Operation();
}

class BigBank : IBank
{
    public void Operation()
    {
        Console.WriteLine("BigBank operation...");
    }
}

class SmallBank : IBank
{
    public void Operation()
    {
        Console.WriteLine("SmallBank operation...");
    }
}

interface IAccountBridge
{
    void Process();
}

class SavingsAccountBridge : IAccountBridge
{
    protected IBank _bank;
    public SavingsAccountBridge(IBank bank) => _bank = bank;
    public void Process()
    {
        Console.WriteLine("Processing savings account.");
        _bank.Operation();
    }
}

class CheckingAccountBridge : IAccountBridge
{
    protected IBank _bank;
    public CheckingAccountBridge(IBank bank) => _bank = bank;
    public void Process()
    {
        Console.WriteLine("Processing checking account.");
        _bank.Operation();
    }
}

//Usage:
IBank bank = new BigBank();
IAccountBridge account = new SavingsAccountBridge(bank);
account.Process();
```

3. ### Composite

**Descrição:** Compõe objetos em estruturas de árvore para representar hierarquias parte-todo.

**Intenção:**  Ideal para representar hierarquias de objetos.

**Exemplo Prático:**
```csharp
interface IFinancialProduct
{
    void Display();
}

class BankAccount : IFinancialProduct
{
    public void Display() => Console.WriteLine("This is a bank account.");
}

class CreditCard : IFinancialProduct
{
    public void Display() => Console.WriteLine("This is a credit card.");
}

class FinancialProductComposite : IFinancialProduct
{
    private readonly List<IFinancialProduct> _products = new List<IFinancialProduct>();

    public void Add(IFinancialProduct product) => _products.Add(product);
    public void Display()
    {
        foreach (var product in _products)
        {
            product.Display();
        }
    }
}

//Usage: 
IFinancialProduct account = new BankAccount();
IFinancialProduct card = new CreditCard();

FinancialProductComposite composite = new FinancialProductComposite();
composite.Add(account);
composite.Add(card);

composite.Display();
```

4. ### Decorator

**Descrição:**  Ideal para representar hierarquias de objetos.

**Intenção:**  Ideal para representar hierarquias de objetos.

**Exemplo Prático:**
```csharp
interface IBankAccount
{
    void Operate();
}

class SimpleBankAccount : IBankAccount
{
    public void Operate() => Console.WriteLine("Simple bank account operation.");
}

class InsuranceDecorator : IBankAccount
{
    private IBankAccount _account;

    public InsuranceDecorator(IBankAccount account) => _account = account;

    public void Operate()
    {
        _account.Operate();
        Console.WriteLine("Added insurance feature.");
    }
}

//Usage:
IBankAccount account = new SimpleBankAccount();
account = new InsuranceDecorator(account);
account.Operate();
```

5. ### Facade

**Descrição:** Fornece uma interface unificada para um conjunto de interfaces em um subsistema.

**Intenção:** Simplifica a interface de um subsistema complexo.

**Exemplo Prático:**

```csharp
class Loan
{
    public bool HasNoBadLoans(Customer c) => true;
}

class Credit
{
    public bool HasGoodCredit(Customer c) => true;
}

class Customer { }

class BankFacade
{
    private Loan _loan;
    private Credit _credit;

    public BankFacade()
    {
        _loan = new Loan();
        _credit = new Credit();
    }

    public bool IsEligibleForCredit(Customer c)
    {
        return _loan.HasNoBadLoans(c) && _credit.HasGoodCredit(c);
    }
}
//Usage:
BankFacade facade = new BankFacade();
Customer customer = new Customer();
bool eligible = facade.IsEligibleForCredit(customer);
```

6. ### Flyweight

**Descrição:**  Usa compartilhamento para suportar eficientemente grandes quantidades de objetos finos.

**Intenção:** Reduzir o consumo de memória ao compartilhar objetos comuns.

**Exemplo Prático:**

```csharp
class SharedAccountData
{
    public string AccountNumber { get; set; }
}

class BankAccount
{
    private SharedAccountData _sharedData;
    public string AccountHolder { get; set; }

    public BankAccount(SharedAccountData sharedData)
    {
        _sharedData = sharedData;
    }

    public void Display()
    {
        Console.WriteLine($"AccountNumber: {_sharedData.AccountNumber}, AccountHolder: {AccountHolder}");
    }
}

//Usage:
SharedAccountData data = new SharedAccountData { AccountNumber = "123-456" };

BankAccount account1 = new BankAccount(data) { AccountHolder = "John Doe" };
BankAccount account2 = new BankAccount(data) { AccountHolder = "Jane Smith" };

account1.Display();
account2.Display();
```

7. ### Proxy

**Descrição:** Fornece um substituto ou marcador para outro objeto.

**Intenção:** Fornece um substituto ou marcador para outro objeto.

**Exemplo Prático:**

```csharp
interface IBankAccess
{
    void AccessAccount();
}

class RealBankAccess : IBankAccess
{
    public void AccessAccount()
    {
        Console.WriteLine("Accessing bank account...");
    }
}

class BankAccessProxy : IBankAccess
{
    private RealBankAccess _realAccess;

    public void AccessAccount()
    {
        if (_realAccess == null)
        {
            _realAccess = new RealBankAccess();
        }
        _realAccess.AccessAccount();
    }
}

//Usage:
IBankAccess access = new BankAccessProxy();
access.AccessAccount();
```

