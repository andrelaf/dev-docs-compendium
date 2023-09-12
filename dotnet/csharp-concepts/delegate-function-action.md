# Delegate, Action e Function

1. ## Delegate
Definição: Um delegate é um tipo que representa referências a métodos com um tipo de parâmetro e tipo de retorno específicos. Você pode instanciar um delegate e associá-lo a qualquer método com uma assinatura e tipo de retorno compatíveis. Você pode invocar (ou chamar) o método através do delegate.

**Quando usar:** Use delegates quando precisar definir assinaturas de método que serão implementadas em outro lugar. Isso é comum em eventos e callbacks.

**Exemplo:**
```csharp
public delegate int Operacao(int x, int y);

public class Calculadora
{
    public int Somar(int x, int y)
    {
        return x + y;
    }
}

var calc = new Calculadora();
Operacao op = calc.Somar;
int resultado = op(3, 4);  // resultado = 7
```

1. Action
**Definição:** Action é um tipo de delegate (introduzido no .NET Framework 3.5) que representa um método que não tem parâmetros e não retorna valor.

**Quando usar:** Use Action quando quiser passar um método (ou lambda) como parâmetro, e esse método não retorna um valor.

**Exemplo:**
```csharp
public class Notificacao
{
    public void EnviarMensagem()
    {
        Console.WriteLine("Mensagem enviada!");
    }
}

var notificacao = new Notificacao();
Action enviar = notificacao.EnviarMensagem;
enviar();
```
1. Function
   
**Definição:** Func é outro tipo de delegate introduzido no .NET Framework 3.5 que pode ter zero a dezesseis parâmetros de entrada e retorna um valor.

**Quando usar:** Use Func quando quiser passar um método (ou lambda) como parâmetro e esse método retorna um valor.

**Exemplo:**
```csharp
public class Matematica
{
    public int Multiplicar(int a, int b)
    {
        return a * b;
    }
}

var mat = new Matematica();
Func<int, int, int> operacao = mat.Multiplicar;
int produto = operacao(3, 4);  // produto = 12
```

## Vamos criar um exemplo que utiliza Delegate, Action e Func em um aplicativo ASP.NET Core

```csharp
public class MessageService
{
    // Usando Delegate para definir um tipo específico para processar mensagens
    public delegate void MessageDelegate(string message);

    // Método que usa o Delegate
    public void ProcessMessage(MessageDelegate handler, string message)
    {
        handler(message);
    }

    // Método que usa Action
    public void ProcessActionMessage(Action<string> actionHandler, string message)
    {
        actionHandler(message);
    }

    // Método que usa Func
    public string ProcessFuncMessage(Func<string, string> funcHandler, string message)
    {
        return funcHandler(message);
    }
}

[ApiController]
[Route("api/message")]
public class MessageController : ControllerBase
{
    private readonly MessageService _messageService;

    public MessageController(MessageService messageService)
    {
        _messageService = messageService;
    }

    [HttpGet("delegate")]
    public IActionResult UseDelegate()
    {
        _messageService.ProcessMessage(LogMessage, "Message using Delegate");
        return Ok();
    }

    [HttpGet("action")]
    public IActionResult UseAction()
    {
        _messageService.ProcessActionMessage(LogMessage, "Message using Action");
        return Ok();
    }

    [HttpGet("func")]
    public IActionResult UseFunc()
    {
        var response = _messageService.ProcessFuncMessage(FormatMessage, "Message using Func");
        return Ok(response);
    }

    private void LogMessage(string message)
    {
        // Aqui você pode logar a mensagem onde preferir (console, arquivo, etc)
        Console.WriteLine(message);
    }

    private string FormatMessage(string message)
    {
        // Suponhamos que queremos formatar a mensagem antes de registrá-la
        return $"[INFO] - {message}";
    }
}
```