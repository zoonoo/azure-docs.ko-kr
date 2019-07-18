---
title: 빠른 시작 - Azure PowerShell을 사용하여 Service Bus 큐 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Service Bus큐를 만드는 방법을 배웁니다. 그런 다음, 애플리케이션 샘플을 사용하여 큐에서 메시지를 보내고 받습니다.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 80cef3efd65a3f68dba1856a892a9a120f7d95cd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990293"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>빠른 시작: Azure PowerShell을 사용하여 Service Bus 큐 만들기
이 빠른 시작에서는 PowerShell을 사용하여 메시징 네임스페이스 및 해당 네임스페이스 내에 큐를 만들고 네임스페이스에서 권한 부여 자격 증명을 얻은 다음, Service Bus 큐에서 메시지를 보내고 받는 방법을 보여 줍니다. 그런 다음, [.NET 표준 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)를 사용하여 이 큐에서 메시지를 보내고 받는 방법을 보여 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음을 설치했어야 합니다.

- Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][]을 만듭니다. 
- [Visual Studio 2017 업데이트 3(버전 15.3, 26730.01)](https://www.visualstudio.com/vs) 이상 Visual Studio를 사용하여 큐로 메시지를 보내고 큐에서 메시지를 받는 샘플을 빌드합니다. 이 샘플은 포털에서 만든 큐를 테스트하는 것입니다. 
- [NET Core SDK](https://www.microsoft.com/net/download/windows) 버전 2.0 이상

이 빠른 시작에서는 최신 버전의 Azure PowerShell을 실행해야 합니다. 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성][]을 참조하세요. Azure Cloud Shell에 익숙한 경우 머신에 Azure PowerShell을 설치하지 않고 사용할 수 있습니다. Azure Cloud Shell에 대한 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

1. 먼저 Service Bus PowerShell 모듈을 아직 설치하지 않았으면 이를 설치합니다.

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. 다음 명령을 실행하여 Azure에 로그인합니다.

   ```azurepowershell-interactive
   Login-AzAccount
   ```

3. 다음 명령을 실행하여 현재 구독 컨텍스트를 설정하거나 현재 활성 구독을 확인합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>리소스 프로비전

PowerShell 프롬프트에서 다음 명령을 실행하여 Service Bus 리소스를 프로비전합니다. 모든 자리 표시자를 적절한 값으로 바꿔야 합니다.

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

`Get-AzServiceBusKey` cmdlet이 실행되면 선택한 연결 문자열과 큐 이름을 복사하여 메모장과 같은 임시 위치에 붙여넣습니다. 이는 다음 단계에서 필요합니다.

## <a name="send-and-receive-messages"></a>메시지 보내기 및 받기

네임스페이스와 큐가 만들어지고 필요한 자격 증명이 있으면 메시지를 보내고 받을 준비가 됩니다. [이 GitHub 샘플 폴더](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart)에서 코드를 검사할 수 있습니다.

코드를 실행하려면 다음을 수행합니다.

1. 다음 명령을 실행하여 [Service Bus GitHub 리포지토리](https://github.com/Azure/azure-service-bus/)를 복제합니다.

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. 다음 샘플 폴더로 이동합니다. `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`

4. 아직 연결 문자열을 가져오지 않은 경우 다음 PowerShell cmdlet을 사용하여 연결 문자열을 가져옵니다. `my-resourcegroup`  및  `namespace-name`을 각자 해당하는 값으로 바꿔야 합니다. 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5. PowerShell 프롬프트에서 다음 명령을 입력합니다.

   ```shell
   dotnet build
   ```

6. `bin\Debug\netcoreapp2.0` 폴더로 이동합니다.

7. 다음 명령을 입력하여 프로그램을 실행합니다. `myConnectionString`을 앞에서 가져온 값으로 바꾸고, `myQueueName`을 만든 큐의 이름으로 바꿔야 합니다.

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. 큐에 보낸 10개 메시지를 관찰한 후에 큐에서 받습니다.

   ![프로그램 출력](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 명령을 실행하여 리소스 그룹, 네임스페이스 및 관련된 모든 리소스를 제거합니다.

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>샘플 코드 이해

이 섹션에는 샘플 코드의 기능에 대한 자세한 내용이 포함되어 있습니다. 

### <a name="get-connection-string-and-queue"></a>연결 문자열 및 큐 가져오기

연결 문자열과 큐 이름은 명령줄 인수로 `Main()` 메서드에 전달됩니다. `Main()`은 다음과 같이 이러한 값을 보유할 두 개의 문자열 변수를 선언합니다.

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
그런 다음, `Main()` 메서드에서 `MainAsync()` 비동기 메시지 루프를 시작합니다.

### <a name="message-loop"></a>메시지 루프

MainAsync() 메서드는 명령줄 인수를 사용하여 큐 클라이언트를 만들고, `RegisterOnMessageHandlerAndReceiveMessages()`라는 수신 메시지 처리기를 호출하고, 메시지 세트를 보냅니다.

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

`RegisterOnMessageHandlerAndReceiveMessages()` 메서드는 단순히 몇 가지 메시지 처리기 옵션을 설정한 다음, 큐 클라이언트의 `RegisterMessageHandler()` 메서드를 호출하여 수신을 시작합니다.

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>메시지 보내기

메시지 만들기 및 보내기 작업은 `SendMessagesAsync()` 메서드에서 수행됩니다.

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>메시지 처리

`ProcessMessagesAsync()` 메서드는 메시지 수신의 승인, 처리 및 완료를 수행합니다.

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, 알림 허브 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 큐에서 메시지를 보내고 받는 데 필요한 Service Bus 네임스페이스 및 기타 리소스를 만들었습니다. 메시지를 보내고 받기 위한 코드 작성에 대해 자세히 알아보려면 **메시지 보내기 및 받기** 섹션의 자습서로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [메시지 보내기 및 받기](service-bus-dotnet-get-started-with-queues.md)

[체험 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure PowerShell 설치 및 구성]: /powershell/azure/install-Az-ps
