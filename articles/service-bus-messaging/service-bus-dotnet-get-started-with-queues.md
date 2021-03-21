---
title: Azure Service Bus 큐 시작(Azure.Messaging.ServiceBus)
description: 이 자습서에서는 Service Bus 큐에서 메시지를 보내고 받는 .NET Core C# 애플리케이션을 만듭니다.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec3f53e6f69614028c013efa5f0e6852cbc3f8ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631643"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Azure Service Bus 큐에서 메시지 보내기 및 받기(.NET)
이 자습서에서는 **Azure.Messaging.ServiceBus** 패키지를 사용하여 Azure Service Bus 큐에서 메시지를 보내고 받는 .NET Core 콘솔 애플리케이션을 만듭니다. 

> [!Important]
> 이 빠른 시작에서는 새 Azure.Messaging.ServiceBus 패키지를 사용합니다. 이전 Microsoft.Azure.ServiceBus 패키지를 사용하는 빠른 시작은 [Microsoft.Azure.ServiceBus 패키지를 사용하여 이벤트 보내기 및 받기](service-bus-dotnet-get-started-with-queues-legacy.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- 작업할 큐가 없는 경우 [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다. Service Bus 네임스페이스에 대한 **연결 문자열** 및 만든 **큐** 의 이름을 적어 둡니다.

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
이 빠른 시작에서는 메시지를 큐에 보내는 C# .NET Core 콘솔 애플리케이션을 만듭니다.

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기
Visual Studio를 시작하고, C#용 새 **콘솔 앱(.NET Core)** 프로젝트를 만듭니다. 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리** 를 선택합니다.
1. **찾아보기** 를 선택합니다. **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** 를 검색하여 선택합니다.
1. **설치** 를 선택하여 설치를 완료한 다음, NuGet 패키지 관리자를 닫습니다.

### <a name="add-code-to-send-messages-to-the-queue"></a>메시지를 큐에 보내는 코드 추가

1. *Program.cs* 의 네임스페이스 정의 위쪽에서 다음 `using` 문을 클래스 선언 앞에 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. `Program` 클래스에서 다음 변수를 선언합니다.

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    네임스페이스에 대한 연결 문자열을 `connectionString` 변수로 입력합니다. 큐 이름을 입력합니다.

1. `Main()` 메서드 바로 뒤에 메시지 전송 작업을 수행하는 다음 `SendMessagesAsync()` 메서드를 추가합니다.

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. 메시지의 큐(.NET 큐)를 만드는 `CreateMessages`라는 메서드를 `Program` 클래스에 추가합니다. 일반적으로 이러한 메시지는 애플리케이션의 여러 부분에서 가져옵니다. 여기서는 샘플 메시지의 큐를 만듭니다.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. `SendMessageBatchAsync`라는 메서드를 `Program` 클래스에 추가하고, 다음 코드를 추가합니다. 이 메서드는 메시지의 큐를 사용하고, Service Bus 큐로 보낼 하나 이상의 일괄 처리를 준비합니다. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }
        
                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);
        
                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. `Main()` 메서드를 다음 **async** `Main` 메서드로 바꿉니다. 이 메서드는 단일 메시지 및 메시지 일괄 처리를 큐로 보내는 send 메서드를 모두 호출합니다. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. 애플리케이션을 실행합니다. 다음과 같은 메시지가 표시됩니다. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. Azure Portal에서 다음 단계를 수행합니다.
    1. Service Bus 네임스페이스로 이동합니다. 
    1. **개요** 페이지의 아래쪽 가운데 창에서 큐를 선택합니다. 
    1. **기본 정보** 섹션의 값을 확인합니다.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="개수 및 크기와 함께 받은 메시지" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    다음 값을 확인합니다.
    - 큐에 대한 **활성 메시지 수** 값은 이제 **4** 입니다. 메시지를 검색하지 않고 이 보낸 사람 앱을 실행할 때마다 이 값이 4씩 증가합니다.
    - 앱에서 메시지를 큐에 추가할 때마다 큐의 현재 크기는 **기본 정보** 의 **현재** 값을 증가시킵니다.
    - 아래쪽 **메트릭** 섹션의 **메시지** 차트에서 해당 큐에 대해 4개의 들어오는 메시지가 있음을 확인할 수 있습니다. 

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
이 섹션에서는 큐에서 메시지를 검색하는 코드를 추가합니다.

1. 메시지 및 오류를 처리하는 다음 메서드를 `Program` 클래스에 추가합니다. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. `ReceiveMessagesAsync`라는 메서드를 `Program` 클래스에 추가하고, 메시지를 받는 다음 코드를 추가합니다. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. `Main` 메서드에서 `ReceiveMessagesAsync` 메서드에 대한 호출을 추가합니다. 메시지 수신만 테스트하려면 `SendMessagesAsync` 메서드를 주석으로 처리합니다. 그렇지 않으면 큐로 보낸 다른 4개의 메시지가 표시됩니다. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>앱 실행
애플리케이션을 실행합니다. 잠시 기다린 다음, 아무 키나 눌러 메시지 수신을 중지합니다. 다음 출력이 표시됩니다(스페이스바 키 사용). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

포털을 다시 확인합니다. 

- **활성 메시지 수** 및 **현재** 값은 이제 **0** 입니다.
- 아래쪽 **메트릭** 섹션의 **메시지** 차트에서 해당 큐에 대해 8개의 들어오는 메시지와 8개의 나가는 메시지가 있음을 확인할 수 있습니다. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="받은 후의 활성 메시지 수 및 크기" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요.

- [.NET용 Azure Service Bus 클라이언트 라이브러리 - 추가 정보](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 참조](/dotnet/api/azure.messaging.servicebus?preserve-view=true)