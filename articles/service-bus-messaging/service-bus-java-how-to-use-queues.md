---
title: Java에서 Azure Service Bus 큐 사용(azure-messaging-servicebus)
description: 이 자습서에서는 Java를 사용하여 Azure Service Bus 큐에서 메시지를 보내고 받는 방법을 알아봅니다. 새 azure-messaging-servicebus 패키지를 사용합니다.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: a910f61389183b77af1f73f8d3553f6c5bbc8452
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881587"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Azure Service Bus 큐에서 메시지 보내기 및 받기(Java)
이 빠른 시작에서는 Azure Service Bus 큐에서 메시지를 보내고 받는 Java 앱을 만듭니다. 

> [!IMPORTANT]
> 이 빠른 시작에서는 새 azure-messaging-servicebus 패키지를 사용합니다. 이전 azure-servicebus 패키지를 사용하는 빠른 시작은 [azure-servicebus를 사용하여 메시지 보내기 및 받기](service-bus-java-how-to-use-queues-legacy.md)를 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소
- Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- 작업할 큐가 없는 경우 [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다. Service Bus 네임스페이스에 대한 **연결 문자열** 및 만든 **큐** 의 이름을 적어 둡니다.
- [Java용 Azure SDK][Azure SDK for Java]를 설치합니다. Eclipse를 사용하는 경우 Java용 Azure SDK가 포함된 [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]를 설치할 수 있습니다. 그런 다음, **Java용 Microsoft Azure 라이브러리** 를 프로젝트에 추가할 수 있습니다. IntelliJ를 사용하는 경우 [Azure Toolkit for IntelliJ 설치](/azure/developer/java/toolkit-for-intellij/installation)를 참조하세요. 


## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
이 섹션에서는 Java 콘솔 프로젝트를 만들고, 메시지를 이전에 만든 큐에 보내는 코드를 추가합니다. 

### <a name="create-a-java-console-project"></a>Java 콘솔 프로젝트 만들기
Eclipse 또는 원하는 도구를 사용하여 Java 프로젝트를 만듭니다. 

### <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 애플리케이션 구성
Azure Service Bus 라이브러리에 대한 참조를 추가합니다. Service Bus용 Java 클라이언트 라이브러리는 [Maven Central Repository](https://search.maven.org/search?q=a:azure-messaging-servicebus)에서 사용할 수 있습니다. Maven 프로젝트 파일 안에 다음 종속성 선언을 사용하여 이 라이브러리를 참조할 수 있습니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>메시지를 큐에 보내는 코드 추가
1. 다음 `import` 문을 Java 파일의 토픽에 추가합니다. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. 클래스에서 아래와 같이 연결 문자열 및 큐 이름을 저장할 변수를 정의합니다. 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다. 그리고 `<QUEUE NAME>`을 큐 이름으로 바꿉니다.
3. 하나의 메시지를 큐에 보내는 `sendMessage`라는 메서드를 클래스에 추가합니다. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. 메시지 목록을 만드는 `createMessages`라는 메서드를 클래스에 추가합니다. 일반적으로 이러한 메시지는 애플리케이션의 여러 부분에서 가져옵니다. 여기서는 샘플 메시지의 목록을 만듭니다.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. 만든 큐에 메시지를 보내는 `sendMessageBatch`라는 메서드를 추가합니다. 이 메서드는 큐에 대한 `ServiceBusSenderClient`를 만들고, `createMessages` 메서드를 호출하여 메시지 목록을 가져오고, 하나 이상의 일괄 처리를 준비하고, 해당 일괄 처리를 큐에 보냅니다. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
이 섹션에서는 큐에서 메시지를 검색하는 코드를 추가합니다. 

1. 큐에서 메시지를 받는 `receiveMessages`라는 메서드를 추가합니다. 이 메서드는 메시지를 처리하기 위한 처리기 및 오류를 처리하기 위한 다른 처리기를 지정하여 큐에 대한 `ServiceBusProcessorClient`를 만듭니다. 그런 다음, 프로세서를 시작하고, 몇 초 동안 기다리고, 받은 메시지를 출력한 다음, 프로세서를 중지하고 닫습니다.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }    
    ```
2. `sendMessage`, `sendMessageBatch` 및 `receiveMessages` 메서드를 호출하고 `InterruptedException`을 throw하도록 `main` 메서드를 업데이트합니다.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>앱 실행
애플리케이션을 실행하면 콘솔 창에 다음 메시지가 표시됩니다. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

Service Bus 네임스페이스에 대한 Azure Portal의 **개요** 페이지에서 **들어오는 메시지** 및 **나가는 메시지** 의 수를 확인할 수 있습니다. 1분 정도 기다린 다음, 페이지를 새로 고쳐 최신 값을 확인해야 할 수도 있습니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="들어오는 메시지 및 나가는 메시지의 수" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

이 **개요** 페이지에서 큐를 선택하여 **Service Bus 큐** 페이지로 이동합니다. 이 페이지에도 **들어오는 메시지** 및 **나가는 메시지** 의 수가 표시됩니다. 또한 큐의 **현재 크기**, **최대 크기**, **활성 메시지 수** 등과 같은 다른 정보도 표시됩니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="큐 세부 정보" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요.

- [Java용 Azure Service Bus 클라이언트 라이브러리 - 추가 정보](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub 샘플](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage