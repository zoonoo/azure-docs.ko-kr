---
title: azure-messaging-servicebus를 사용하여 Azure Service Bus 토픽에 메시지 보내기
description: 이 빠른 시작에서는 azure-messaging-servicebus 패키지를 사용하여 메시지를 Azure Service Bus 토픽에 보내는 방법을 보여 줍니다.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 79eb7783fd3daf546539dd5b9048f4e9f484374f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279802"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Azure Service Bus 토픽에 메시지 보내기 및 토픽의 구독에서 메시지 받기(.NET)
이 자습서에서는 다음 작업을 수행하는 C# 애플리케이션을 만듭니다.

1. Service Bus 항목에 메시지를 보냅니다. 

    Service Bus 항목은 보낸 사람 엔드포인트에서 메시지를 보낼 수 있는 엔드포인트를 제공합니다. 토픽은 하나 이상의 구독을 가질 수 있습니다. 토픽의 각 구독은 토픽에 전송된 메시지의 복사본을 가져옵니다. Service Bus 토픽에 대한 자세한 내용은 [Azure Service Bus란?](service-bus-messaging-overview.md)을 참조하세요. 
1. 토픽의 구독에서 메시지 받기. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Service Bus 토픽 및 구독":::

    > [!Important]
    > 이 빠른 시작에서는 새 **Azure.Messaging.ServiceBus** 패키지를 사용합니다. 이전 Microsoft.Azure.ServiceBus 패키지를 사용하는 경우 [Microsoft.Azure.ServiceBus 패키지를 사용하여 메시지 보내기 및 받기](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- 이 [빠른 시작](service-bus-quickstart-topics-subscriptions-portal.md)의 단계를 따라 Service Bus 토픽 및 해당 토픽에 대한 구독을 만듭니다. 

    > [!NOTE]
    > 네임스페이스에 대한 연결 문자열, 토픽 이름 및 이 자습서의 토픽에 대한 구독 중 하나의 이름을 사용합니다.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기
이 섹션에서는 Visual Studio에서 .NET Core 콘솔 애플리케이션을 만들고, 만든 Service Bus 토픽에 메시지를 보내는 코드를 추가합니다. 

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기
Visual Studio를 사용하여 .NET Core 콘솔 애플리케이션 만들기. 

1. Visual Studio를 실행합니다.  
1. **시작** 페이지가 표시되면 **새 프로젝트 만들기** 를 선택합니다. 
1. **새 프로젝트 만들기** 페이지에서 다음 단계를 수행합니다. 
    1. 프로그래밍 언어에서 **C#** 을 선택합니다. 
    1. 프로젝트 형식에서 **콘솔** 을 선택합니다. 
    1. 템플릿 목록에서 **콘솔 앱(.NET Core)** 을 선택합니다. 
    1. 그다음에 **다음** 을 선택합니다. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="콘솔 앱 프로젝트 만들기":::
1. **새 프로젝트 구성** 페이지에서 이 단계를 수행합니다. 
    1. **프로젝트 이름** 에 프로젝트의 이름을 입력합니다. 
    1. **위치** 에서 프로젝트 및 솔루션 파일의 위치를 선택합니다. 
    1. **솔루션 이름** 에서 솔루션의 이름을 입력합니다. Visual Studio 솔루션에는 하나 이상의 프로젝트가 포함될 수 있습니다. 이 빠른 시작에서 솔루션에는 프로젝트가 하나만 있습니다. 
    1. **만들기** 를 선택하여 프로젝트를 만듭니다. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="프로젝트 및 솔루션 이름 및 위치 입력":::    


### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가
1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리** 를 선택합니다.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="NuGet 패키지 관리 메뉴":::        
1. **찾아보기** 탭으로 전환합니다.
1. **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** 를 검색하여 선택합니다.
1. **설치** 을 선택하여 설치를 완료합니다.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Service Bus NuGet 패키지를 선택합니다.":::
5. **변경 내용 미리 보기** 대화 상자가 표시되면 **확인** 을 선택하여 계속합니다. 
1. **라이선스 승인** 페이지가 표시되면 **동의함** 을 선택하여 계속합니다. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>코드를 추가하여 토픽에 메시지 보내기 

1. **솔루션 탐색기** 창에서 **Program.cs** 를 두 번 클릭하여 편집기에서 파일을 엽니다. 
1. 클래스 선언 앞에 네임스페이스 정의 맨 위에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. `Program` 클래스에서 `Main` 함수 위에 다음 변수를 선언합니다.

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    다음 값을 바꿉니다.
    - `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다.
    - `<TOPIC NAME>`을 토픽 이름으로 바꿉니다.
    - `<SUBSCRIPTION NAME>`을 구독 이름으로 바꿉니다.

### <a name="send-a-single-message-to-the-topic"></a>토픽에 단일 메시지 보내기
`SendMessageToTopicAsync`라는 메서드를 `Main` 메서드의 위 또는 아래에 있는 `Program` 클래스에 추가합니다. 이 메서드는 토픽에 단일 메시지를 보냅니다.

```csharp
    static async Task SendMessageToTopicAsync()
    {
        // create a Service Bus client 
        await using (ServiceBusClient client = new ServiceBusClient(connectionString))
        {
            // create a sender for the topic
            ServiceBusSender sender = client.CreateSender(topicName);
            await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
            Console.WriteLine($"Sent a single message to the topic: {topicName}");
        }
    }
```

이 메서드는 다음 단계를 수행합니다. 
1. 네임스페이스에 대한 연결 문자열을 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체를 만듭니다. 
1. `ServiceBusClient` 개체를 사용하여 지정된 Service Bus 항목에 대한 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) 개체를 만듭니다. 이 단계는 [ServiceBusClient.CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) 메서드를 사용합니다.
1. 그런 다음, 이 메서드는 [ServiceBusSender.SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) 메서드를 사용하여 단일 테스트 메시지를 Service Bus 토픽으로 보냅니다. 

### <a name="send-a-batch-of-messages-to-the-topic"></a>메시지 일괄 처리를 토픽에 보내기
1. `Program` 클래스에 메시지 큐(Service Bus 큐가 아닌 .NET 큐)를 만들려면 `CreateMessages`라는 메서드를 추가합니다. 일반적으로 이러한 메시지는 애플리케이션의 여러 부분에서 가져옵니다. 여기서는 샘플 메시지의 큐를 만듭니다. .NET 큐에 익숙하지 않은 경우 [Queue.Enqueue](/dotnet/api/system.collections.queue.enqueue)를 참조하세요.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. `SendMessageBatchAsync`라는 메서드를 `Program` 클래스에 추가하고, 다음 코드를 추가합니다. 이 메서드는 메시지의 큐를 사용하고, Service Bus 토픽으로 보낼 하나 이상의 일괄 처리를 준비합니다. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```    

    다음은 코드의 중요한 단계입니다.
    1. 네임스페이스에 대한 연결 문자열을 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체를 만듭니다. 
    1. `ServiceBusClient` 개체에서 [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) 메서드를 호출하여 지정된 Service Bus 토픽에 대한 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) 개체를 만듭니다. 
    1. 도우미 메서드 `GetMessages`를 호출하여 Service Bus 토픽으로 보낼 메시지 큐를 가져옵니다. 
    1. [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync)를 사용하여 [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch)를 만듭니다.
    1. [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage)를 사용하여 일괄 처리에 메시지를 추가합니다. 메시지가 일괄 처리에 추가되면 .NET 큐에서 제거됩니다. 
    1. [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) 메서드를 사용하여 메시지 일괄 처리를 Service Bus 토픽에 보냅니다.

### <a name="update-the-main-method"></a>main 메서드 업데이트
`Main()` 메서드를 다음 **async** `Main` 메서드로 바꿉니다. 이 메서드는 단일 메시지 및 메시지 일괄 처리를 토픽으로 보내는 send 메서드를 모두 호출합니다.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>앱을 테스트하여 메시지를 토픽에 보내기
1. 애플리케이션을 실행합니다. 다음과 같은 출력이 표시됩니다.

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. Azure Portal에서 다음 단계를 수행합니다.
    1. Service Bus 네임스페이스로 이동합니다. 
    1. **개요** 페이지의 아래쪽 가운데 창에서 **토픽** 탭으로 전환하고, Service Bus 토픽을 선택합니다. 다음 예에서는 `mytopic`입니다.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="항목 선택":::
    1. **Service Bus 토픽** 페이지 아래쪽의 **메트릭** 섹션에 있는 **메시지** 차트에서 해당 토픽에 대해 4개의 들어오는 메시지가 있음을 확인할 수 있습니다. 값이 표시되지 않으면 몇 분 동안 기다렸다가 페이지를 새로 고쳐 업데이트된 차트를 확인합니다. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="토픽으로 보낸 메시지" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. 아래쪽 창에서 구독을 선택합니다. 다음 예에서는 **S1** 입니다. **Service Bus 구독** 페이지에 **활성 메시지 수** 가 **4** 로 표시됩니다. 구독에서 토픽으로 보낸 4개의 메시지를 받았지만 받는 사람이 아직 이를 선택하지 않았습니다. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="구독에서 받은 메시지" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기

1. 메시지 및 오류를 처리하는 다음 메서드를 `Program` 클래스에 추가합니다. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. 다음 `ReceiveMessagesFromSubscriptionAsync` 메서드를 `Program` 클래스에 추가합니다.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

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

    다음은 코드의 중요한 단계입니다.
    1. 네임스페이스에 대한 연결 문자열을 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체를 만듭니다. 
    1. `ServiceBusClient` 개체에서 [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) 메서드를 호출하여 지정된 Service Bus 토픽 및 구독 조합에 대해 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 개체를 생성합니다. 
    1. `ServiceBusProcessor` 개체의 [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) 및 [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) 이벤트에 대한 처리기를 지정합니다. 
    1. `ServiceBusProcessor` 개체에서 [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync)를 호출하여 메시지 처리를 시작합니다. 
    1. 사용자가 키를 눌러 처리를 종료하면 `ServiceBusProcessor` 객체에서 [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync)를 호출합니다. 
1. `ReceiveMessagesFromSubscriptionAsync` 메서드에 대한 호출을 `Main` 메서드에 추가합니다. 메시지 수신만 테스트하려면 `SendMessagesToTopicAsync` 메서드를 주석으로 처리합니다. 그렇지 않으면 토픽으로 보낸 다른 4개의 메시지가 표시됩니다. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>앱 실행
애플리케이션을 실행합니다. 잠시 기다린 다음, 아무 키나 눌러 메시지 수신을 중지합니다. 다음 출력이 표시됩니다(스페이스바 키 사용). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

포털을 다시 확인합니다. 

- **Service Bus 토픽** 페이지의 **메시지** 차트에 8개의 들어오는 메시지와 8개의 나가는 메시지가 표시됩니다. 이러한 숫자가 표시되지 않으면 몇 분 동안 기다렸다가 페이지를 새로 고쳐 업데이트된 차트를 확인합니다. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="보낸 메시지 및 받은 메시지" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- **Service Bus 구독** 페이지에 **활성 메시지 수** 가 0으로 표시됩니다. 받는 사람이 이 구독에서 메시지를 받고 메시지를 완료했기 때문입니다. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="종료된 구독의 활성 메시지 수" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요.

- [.NET용 Azure Service Bus 클라이언트 라이브러리 - 추가 정보](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub의 Azure Service Bus에 대한 .NET 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 참조](/dotnet/api/azure.messaging.servicebus?preserve-view=true)