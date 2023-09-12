## Comportamentais

1. ### Chain of Responsibility

**Descrição:** Desacopla o remetente de uma solicitação de seus destinatários.

**Intenção:** Permite que vários objetos tratem a solicitação ou passem a solicitação adiante.

**Exemplo Prático:**
```csharp
public abstract class LoanHandler
{
    protected LoanHandler NextHandler;

    public void SetNext(LoanHandler handler)
    {
        NextHandler = handler;
    }

    public abstract void HandleLoanApproval(int amount);
}

public class Manager : LoanHandler
{
    public override void HandleLoanApproval(int amount)
    {
        if (amount <= 10000)
            Console.WriteLine("Loan approved by Manager");
        else if (NextHandler != null)
            NextHandler.HandleLoanApproval(amount);
    }
}

public class Director : LoanHandler
{
    public override void HandleLoanApproval(int amount)
    {
        if (amount <= 50000)
            Console.WriteLine("Loan approved by Director");
        else if (NextHandler != null)
            NextHandler.HandleLoanApproval(amount);
    }
}

// Usage
var manager = new Manager();
var director = new Director();
manager.SetNext(director);
manager.HandleLoanApproval(30000);

```
2. ### Command

**Descrição:** Encapsula uma solicitação como um objeto.

**Intenção:** Permite desacoplar o objeto que envia uma solicitação do objeto que realiza a operação.

**Exemplo Prático:**

```csharp
public interface ICommand
{
    void Execute();
}

public class DepositCommand : ICommand
{
    private readonly BankAccount _account;
    private readonly double _amount;

    public DepositCommand(BankAccount account, double amount)
    {
        _account = account;
        _amount = amount;
    }

    public void Execute()
    {
        _account.Deposit(_amount);
    }
}

public class BankAccount
{
    public void Deposit(double amount)
    {
        // Deposit logic
    }
}

public class WithdrawCommand : ICommand
{
    private readonly BankAccount _account;
    private readonly double _amount;

    public WithdrawCommand(BankAccount account, double amount)
    {
        _account = account;
        _amount = amount;
    }

    public void Execute()
    {
        _account.Withdraw(_amount);
    }
}

// Usage:

var account = new BankAccount();
var deposit = new DepositCommand(account, 1000);
var withdraw = new WithdrawCommand(account, 500);
deposit.Execute();
withdraw.Execute();
```
3. ### Interpreter

**Descrição:**  Fornece uma representação de sua gramática e interpreta sentenças dessa gramática.

**Intenção:**  Fornece uma representação de sua gramática e interpreta sentenças dessa gramática.

**Exemplo Prático:**

```csharp
// Interface de expressão
public interface IExpressao
{
    int Interpretar();
}

// Classe TerminalExpression para números
public class Numero : IExpressao
{
    private int _numero;

    public Numero(int numero)
    {
        _numero = numero;
    }

    public int Interpretar()
    {
        return _numero;
    }
}

// Classe NonTerminalExpression para soma
public class Soma : IExpressao
{
    private IExpressao _esquerda, _direita;

    public Soma(IExpressao esquerda, IExpressao direita)
    {
        _esquerda = esquerda;
        _direita = direita;
    }

    public int Interpretar()
    {
        return _esquerda.Interpretar() + _direita.Interpretar();
    }
}

// Classe NonTerminalExpression para subtração
public class Subtracao : IExpressao
{
    private IExpressao _esquerda, _direita;

    public Subtracao(IExpressao esquerda, IExpressao direita)
    {
        _esquerda = esquerda;
        _direita = direita;
    }

    public int Interpretar()
    {
        return _esquerda.Interpretar() - _direita.Interpretar();
    }
}

// Usage: 

 IExpressao cinco = new Numero(5);
IExpressao tres = new Numero(3);
IExpressao oito = new Numero(8);

IExpressao soma = new Soma(cinco, tres);  // 5 + 3
IExpressao subtracao = new Subtracao(oito, tres);  // 8 - 3

Console.WriteLine(soma.Interpretar());  // 8
Console.WriteLine(subtracao.Interpretar());  // 5
```
4. ### Iterator

**Descrição:** Fornece uma maneira de acessar os elementos de um objeto agregado sequencialmente.

**Intenção:** Usar quando se deseja fornecer acesso a uma coleção de objetos sem expor sua estrutura subjacente.

**Exemplo Prático:**

```csharp
public class BankAccounts : IEnumerable<Account>
{
    private readonly List<Account> _accounts;

    public BankAccounts()
    {
        _accounts = new List<Account>();
    }

    public void AddAccount(Account account)
    {
        _accounts.Add(account);
    }

    public IEnumerator<Account> GetEnumerator()
    {
        return _accounts.GetEnumerator();
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
}

public class Account
{
    public string Name { get; set; }
    public double Balance { get; set; }
}

// Usage:

var bankAccounts = new BankAccounts();
bankAccounts.AddAccount(new Account { Name = "John", Balance = 1000 });
foreach (var account in bankAccounts)
{
    Console.WriteLine($"Account Name: {account.Name}, Balance: {account.Balance}");
}

```
5. ### Mediator

**Descrição:** Define um objeto que encapsula como um conjunto de objetos interage.

**Intenção:** Reduzir o acoplamento entre classes, centralizando comunicações externas.

**Exemplo Prático:**

```csharp
public interface IMediator
{
    void Notify(object sender, string message);
}

public class BankMediator : IMediator
{
    private Customer _customer;
    private BankService _bankService;

    public BankMediator(Customer customer, BankService bankService)
    {
        _customer = customer;
        _bankService = bankService;
    }

    public void Notify(object sender, string message)
    {
        if (sender is Customer)
            _bankService.Receive(message);
        else
            _customer.Receive(message);
    }
}

public class Customer
{
    private IMediator _mediator;

    public Customer(IMediator mediator)
    {
        _mediator = mediator;
    }

    public void Send(string message)
    {
        _mediator.Notify(this, message);
    }

    public void Receive(string message)
    {
        Console.WriteLine($"Customer received: {message}");
    }
}

//Usage:
var customer = new Customer(null);
var bankService = new BankService();
var mediator = new BankMediator(customer, bankService);
customer.Send("I want to open an account.");
```
6. ### Memento

**Descrição:** Captura e externaliza o estado interno de um objeto.

**Intenção:** Permite que um objeto retorne a esse estado posteriormente.

**Exemplo Prático:**
```csharp
public class Account
{
    public double Balance { get; set; }

    public AccountMemento SaveState()
    {
        return new AccountMemento(Balance);
    }

    public void RestoreState(AccountMemento memento)
    {
        Balance = memento.Balance;
    }
}

public class AccountHistory
{
    private Stack<AccountMemento> _history = new Stack<AccountMemento>();

    public void Save(Account account)
    {
        _history.Push(account.SaveState());
    }

    public void Restore(Account account)
    {
        if (_history.Any())
            account.RestoreState(_history.Pop());
    }
}

public class AccountMemento
{
    public double Balance { get; }

    public AccountMemento(double balance)
    {
        Balance = balance;
    }
}

// Usage:
// Usage
var account = new Account();
var history = new AccountHistory();
account.Deposit(100);
history.Save(account);
account.Deposit(50);
history.Restore(account);
```
7. ### Observer

**Descrição:**  Define uma dependência entre objetos para que quando um objeto muda de estado, todos os seus dependentes são notificados.

**Intenção:** Usar quando uma mudança em um objeto precisa ser refletida em outros objetos.

**Exemplo Prático:**

```csharp
ublic interface IObserver
{
    void Update(double balance);
}

public class BankAccount : IObservable
{
    private List<IObserver> _observers = new List<IObserver>();

    public double Balance { get; private set; }

    public void Deposit(double amount)
    {
        Balance += amount;
        Notify();
    }

    public void Attach(IObserver observer)
    {
        _observers.Add(observer);
    }

    public void Detach(IObserver observer)
    {
        _observers.Remove(observer);
    }

    public void Notify()
    {
        foreach (var observer in _observers)
        {
            observer.Update(Balance);
        }
    }
}

public class SMSNotifier : IObserver
{
    public void Update(double balance)
    {
        Console.WriteLine($"SMS notification: Your balance is now {balance}");
    }
}


// Usage:
var account = new BankAccount();
var smsNotifier = new SMSNotifier();
account.Attach(smsNotifier);
account.Deposit(100);

```
8. ### State
   
**Descrição:** Permite que um objeto altere seu comportamento quando seu estado interno muda.

**Intenção:** Permite que um objeto altere seu comportamento quando seu estado interno muda.

**Exemplo Prático:**

```csharp
public interface IAccountState
{
    void Deposit(BankAccount account, double amount);
    void Withdraw(BankAccount account, double amount);
}

public class NormalState : IAccountState
{
    public void Deposit(BankAccount account, double amount)
    {
        account.Balance += amount;
    }

    public void Withdraw(BankAccount account, double amount)
    {
        account.Balance -= amount;
    }
}

public class OverdrawnState : IAccountState
{
    public void Deposit(BankAccount account, double amount)
    {
        account.Balance += amount;
        if (account.Balance > 0)
            account.State = new NormalState();
    }

    public void Withdraw(BankAccount account, double amount)
    {
        throw new Exception("Account overdrawn!");
    }
}

public class BankAccount
{
    public IAccountState State { get; set; }
    public double Balance { get; private set; }

    public BankAccount()
    {
        State = new NormalState();
    }

    public void Deposit(double amount)
    {
        State.Deposit(this, amount);
        CheckState();
    }

    public void Withdraw(double amount)
    {
        State.Withdraw(this, amount);
        CheckState();
    }

    private void CheckState()
    {
        if (Balance < 0)
            State = new OverdrawnState();
        else
            State = new NormalState();
    }
}

// Usage
var account = new BankAccount();
account.Deposit(100);
account.Withdraw(200);

```
9. ### Strategy

**Descrição:** Define uma família de algoritmos, encapsula cada um deles e os torna intercambiáveis.

**Intenção:**  Define uma família de algoritmos, encapsula cada um deles e os torna intercambiáveis.

**Exemplo Prático:**
```csharp
public interface ITransactionStrategy
{
    void Execute();
}

public class TransferStrategy : ITransactionStrategy
{
    public void Execute()
    {
        Console.WriteLine("Executing bank transfer");
    }
}

public class PaymentStrategy : ITransactionStrategy
{
    public void Execute()
    {
        Console.WriteLine("Executing payment");
    }
}

public class BankTransaction
{
    private readonly ITransactionStrategy _strategy;

    public BankTransaction(ITransactionStrategy strategy)
    {
        _strategy = strategy;
    }

    public void ProcessTransaction()
    {
        _strategy.Execute();
    }
}

// Usage:
var transaction = new BankTransaction(new TransferStrategy());
transaction.ProcessTransaction(); 
```
10. ### Template Method
    
**Descrição:** Define o esqueleto de um algoritmo em um método, mas adia algumas etapas para subclasses.

**Intenção:** Define o esqueleto de um algoritmo em um método, mas adia algumas etapas para subclasses.

**Exemplo Prático:**
```csharp
public abstract class BankTemplateMethod
{
    public void MakeTransaction()
    {
        AuthenticateUser();
        Process();
        End();
    }

    public abstract void AuthenticateUser();
    public abstract void Process();

    public void End()
    {
        Console.WriteLine("Transaction finished");
    }
}

public class TransferMethod : BankTemplateMethod
{
    public override void AuthenticateUser()
    {
        Console.WriteLine("Authenticating user for transfer");
    }

    public override void Process()
    {
        Console.WriteLine("Processing bank transfer");
    }
}

// Usage: 
var transfer = new TransferMethod();
transfer.MakeTransaction();
```
11. ### Visitor

**Descrição:** Representa uma operação a ser executada nos elementos de uma estrutura de objeto.

**Intenção:** Usar quando se precisa executar operações variáveis em uma estrutura fixa de objetos.

**Exemplo Prático:**

```csharp
public interface IBankElement
{
    void Accept(IVisitor visitor);
}

public class BankAccount : IBankElement
{
    public double Balance { get; set; }

    public void Accept(IVisitor visitor)
    {
        visitor.Visit(this);
    }
}

public class Loan : IBankElement
{
    public double MonthlyDue { get; set; }

    public void Accept(IVisitor visitor)
    {
        visitor.Visit(this);
    }
}

public interface IVisitor
{
    void Visit(BankAccount account);
    void Visit(Loan loan);
}

public class ReportGenerator : IVisitor
{
    public void Visit(BankAccount account)
    {
        Console.WriteLine($"Generating report for bank account with balance: {account.Balance}");
    }

    public void Visit(Loan loan)
    {
        Console.WriteLine($"Generating report for loan with monthly due: {loan.MonthlyDue}");
    }
}

// Usage:
ar account = new BankAccount { Balance = 1000 };
var loan = new Loan { MonthlyDue = 200 };
var report = new ReportGenerator();

account.Accept(report);
loan.Accept(report);
```


# Recursos Adicionais
1. "Design Patterns: Elements of Reusable Object-Oriented Software" - Gamma, Helm, Johnson, e Vlissides
2. Refactoring.guru - Um excelente recurso online sobre design patterns e refatoração.
Feedback e Suporte
Para dúvidas, sugestões ou relato de problemas, por favor, abra uma issue em nosso repositório.