---
title: Java에서 Azure Service Bus 토픽 및 구독 사용(azure-messaging-servicebus)
description: 이 빠른 시작에서는 azure-messaging-servicebus 패키지를 통해 Java 코드를 작성하여 Azure Service Bus 토픽에 메시지를 보낸 다음, 해당 토픽에 대한 구독에서 메시지를 받습니다.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 46dc6bed7e51a5157d7eb42dac75c0240d440780
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881621"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Azure Service Bus 토픽에 메시지를 보내고 구독에서 토픽으로 메시지 받기(Java)
이 빠른 시작에서는 azure-messaging-servicebus 패키지를 통해 Java 코드를 작성하여 Azure Service Bus 토픽에 메시지를 보낸 다음, 해당 토픽에 대한 구독에서 메시지를 받습니다.

> [!IMPORTANT]
> 이 빠른 시작에서는 새 azure-messaging-servicebus 패키지를 사용합니다. 이전 azure-servicebus 패키지를 사용하는 빠른 시작은 [azure-servicebus를 사용하여 메시지 보내기 및 받기](service-bus-java-how-to-use-topics-subscriptions-legacy.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- [빠른 시작: Azure Portal을 사용하여 Service Bus 토픽 및 해당 토픽에 대한 구독 만들기](service-bus-quickstart-topics-subscriptions-portal.md)의 단계를 따릅니다. 연결 문자열, 항목 이름 및 구독 이름을 기록해 둡니다. 이 빠른 시작에는 하나의 구독만 사용합니다. 
- [Java용 Azure SDK][Azure SDK for Java]를 설치합니다. Eclipse를 사용하는 경우 Azure SDK for Java를 포함하고 있는 [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]를 설치할 수 있습니다. 그런 다음, **Java용 Microsoft Azure 라이브러리** 를 프로젝트에 추가할 수 있습니다. IntelliJ를 사용하는 경우 [Azure Toolkit for IntelliJ 설치](/azure/developer/java/toolkit-for-intellij/installation)를 참조하세요. 


## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기
이 섹션에서는 Java 콘솔 프로젝트를 만들고 사용자가 만든 토픽으로 메시지를 보내는 코드를 추가합니다. 

### <a name="create-a-java-console-project"></a>Java 콘솔 프로젝트 만들기
Eclipse 또는 선택한 도구를 사용하여 Java 프로젝트를 만듭니다. 

### <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 애플리케이션 구성
Azure Service Bus 라이브러리에 대한 참조를 추가합니다. Service Bus용 Java 클라이언트 라이브러리는 [Maven Central 리포지토리](https://search.maven.org/search?q=a:azure-messaging-servicebus)에서 사용할 수 있습니다. Maven 프로젝트 파일 안에 다음 종속성 선언을 사용하여 이 라이브러리를 참조할 수 있습니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>코드를 추가하여 토픽에 메시지 보내기
1. Java 파일 토픽에 다음 `import` 문을 추가합니다. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. 클래스에서 아래와 같이 연결 문자열과 토픽 이름을 저장할 변수를 정의합니다. 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다. 그리고 `<TOPIC NAME>`을 토픽 이름으로 바꿉니다.
3. 클래스에 `sendMessage`라는 메서드를 추가하여 토픽에 메시지 하나를 보냅니다. 

    ```java
        static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. 클래스에 `createMessages`라는 메서드를 추가하여 메시지 목록을 만듭니다. 일반적으로 애플리케이션의 여러 부분에서 이러한 메시지를 가져옵니다. 여기에서 샘플 메시지의 목록을 만듭니다.

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
1. `sendMessageBatch` 메서드라는 메서드를 추가하여 사용자가 만든 토픽으로 메시지를 보냅니다. 이 메서드는 토픽에 대한 `ServiceBusSenderClient`를 만들고, `createMessages` 메서드를 호출하여 메시지 목록을 가져오고, 하나 이상의 일괄 처리를 준비하고, 토픽으로 일괄 처리를 보냅니다. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기
이 섹션에서는 토픽에 대한 구독에서 메시지를 검색하는 코드를 추가합니다. 

1. `receiveMessages`라는 메서드를 추가하여 구독에서 메시지를 받습니다. 이 메서드는 메시지 처리를 위한 처리기와 오류 처리를 위한 처리기를 지정하여 구독에 대한 `ServiceBusProcessorClient`를 만듭니다. 그런 다음, 프로세서를 시작하고 몇 초 동안 대기한 다음, 수신된 메시지를 인쇄하고 프로세서를 중지하고 닫습니다.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
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
2. `main` 메서드를 업데이트하여 `sendMessage`, `sendMessageBatch` 및 `receiveMessages` 메서드를 호출하고 `InterruptedException`을 throw합니다.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>앱 실행
프로그램을 실행하여 다음과 유사한 출력을 확인합니다.

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

Azure Portal의 Service Bus 네임스페이스에 대한 **개요** 페이지에서 **수신** 및 **발신** 메시지 수를 확인할 수 있습니다. 1분 정도 기다렸다가 페이지를 새로 고쳐 최신 값을 확인해야 할 수 있습니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="수신 및 발신 메시지 수" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

가운데 아래쪽 창의 **토픽** 탭으로 전환하고 토픽을 선택하여 해당 토픽에 대한 **Service Bus 토픽** 페이지를 확인합니다. 이 페이지에는 **메시지** 차트에 네 개의 들어오고 나가는 메시지가 표시됩니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="수신 및 발신 메시지" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

`main` 메서드에서 `receiveMessages` 호출을 주석으로 처리하고 앱을 다시 실행하는 경우 **Service Bus 토픽** 페이지에는 들어오는 메시지 8개(4개의 새 메시지)가 표시되지만 나가는 메시지는 4개가 표시됩니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="업데이트된 항목 페이지" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

이 페이지에서 구독을 선택하면 **Service Bus 구독** 페이지로 이동합니다. 이 페이지에서는 활성 메시지 수, 전달 못한 메시지 수 등을 볼 수 있습니다. 이 예제에는 수신자가 아직 받지 않은 4개의 활성 메시지가 있습니다. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="활성 메시지 수" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요.

- [Python용 Azure Service Bus 클라이언트 라이브러리 - 추가 정보](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [GitHub 샘플](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API 참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage