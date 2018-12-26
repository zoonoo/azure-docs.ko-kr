---
title: Java와 함께 Azure Service Bus 항목을 사용하는 방법 | Microsoft Docs
description: Azure에서 Service Bus 토픽 및 구독을 사용합니다.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 09/17/2018
ms.author: spelluru
ms.openlocfilehash: 501d15ebbb373c100dd735e97bebf2f085a9579e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241322"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Java에서 Service Bus 토픽 및 구독을 사용하는 방법

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 빠른 시작에서 수행하는 단계는 다음과 같습니다. 

- Azure Portal을 사용하여 토픽 만들기
- Azure Portal을 사용하여 토픽에 대해 세 가지 구독 만들기
- Java 코드를 작성하여 토픽에 메시지 보내기
- Java 코드를 작성하여 구독에서 메시지 받기

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free).
- [Java용 Azure SDK][Azure SDK for Java]. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus 토픽 및 구독 정의
Service Bus 토픽 및 구독은 *게시/구독* 메시징 통신 모델을 지원합니다. 토픽 및 구독을 사용하는 경우 분산 응용 프로그램의 구성 요소가 서로 직접 통신하지 않고 중간자 역할을 하는 토픽을 통해 메시지를 교환합니다.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

각 메시지가 단일 소비자에 의해 처리되는 Service Bus 큐와 반대로, 토픽과 구독은 게시/구독 패턴을 사용하여 일대다 형태의 통신을 제공합니다. 하나의 토픽에 여러 구독을 등록할 수 있습니다. 토픽에 메시지를 전송하면 각 구독에서 독립적으로 처리할 수 있습니다. 토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 선택적으로 구독별 토픽에 대한 필터 규칙을 등록할 수 있으며, 이렇게 하면 각 토픽 구독에서 받는 토픽 메시지를 필터링하거나 제한할 수 있습니다.

Service Bus 토픽 및 구독을 사용하면 다수의 사용자와 응용 프로그램에 대해 다수의 메시지를 처리하도록 확장할 수 있습니다.

## <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

Service Bus 메시징 네임스페이스는 [정규화된 도메인 이름](https://wikipedia.org/wiki/Fully_qualified_domain_name)으로 참조되는 고유한 범위 지정 컨테이너를 제공하며, 하나 이상의 큐, 토픽 및 구독을 만듭니다. 다음 예제에서는 새 또는 기존 [리소스 그룹](/azure/azure-resource-manager/resource-group-portal)에 Service Bus 메시징 네임스페이스를 만듭니다.

1. 포털의 왼쪽 탐색 창에서 **+ 리소스 만들기**, **엔터프라이즈 통합** 및 **Service Bus**를 차례로 클릭합니다.
2. **네임스페이스 만들기** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.
3. 네임스페이스 이름을 사용할 수 있는지 확인한 후 가격 책정 계층(표준 또는 프리미엄)을 선택합니다.
4. **구독** 필드에서 네임스페이스를 만들 Azure 구독을 선택합니다.
5. **리소스 그룹** 필드에서 네임스페이스가 있는 기존 리소스 그룹을 선택하거나 새로 만듭니다.      
6. **위치**에서 네임스페이스가 호스트되어야 하는 국가 또는 지역을 선택합니다.
7. **만들기**를 클릭합니다. 이제 시스템이 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.

  ![namespace](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>관리 자격 증명 얻기

새 네임 스페이스를 만들면 네임스페이스의 모든 측면에 대한 모든 권한을 부여하는 기본 및 보조 키의 연결된 쌍을 포함한 초기 SAS(공유 액세스 서명) 규칙이 자동으로 생성됩니다. 초기 규칙을 복사하려면 다음 단계를 수행합니다.

1. **모든 리소스**를 클릭한 다음 새로 만든 네임스페이스 이름을 클릭합니다.
2. 네임스페이스 창에서 **공유 액세스 정책**을 클릭합니다.
3. **공유 액세스 정책** 화면에서 **RootManageSharedAccessKey**를 클릭합니다.
4. **정책: RootManageSharedAccessKey** 창에서 **기본 연결 문자열** 옆의 **복사** 단추를 클릭하여 나중에 사용할 수 있도록 연결 문자열을 클립보드에 복사합니다. 메모장이나 기타 다른 위치에 임시로 이 값을 붙여 넣습니다.

    ![connection-string](./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png)
5. 이전 단계를 반복하여 나중에 사용할 수 있도록 **기본 키** 값을 복사하여 임시 위치에 붙여넣습니다.

## <a name="create-a-topic"></a>토픽 만들기 
Service Bus 토픽을 만들려면 해당 토픽을 만들 네임스페이스를 지정합니다. 다음 예제에서는 포털에서 토픽을 만드는 방법을 보여 줍니다.

1. 포털의 왼쪽 탐색 창에서 **Service Bus**(**Service Bus**가 표시되지 않으면 **모든 서비스** 클릭)를 클릭합니다.
2. 토픽을 만들 네임스페이스를 클릭합니다.
3. 네임스페이스 창에서 **토픽**을 클릭한 다음, **토픽** 창에서 **+ 토픽**을 클릭합니다.
4. 토픽 **이름**으로 **BasicTopic**을 입력하고 다른 값은 기본값으로 유지합니다.
5. 창 아래에서 **만들기**를 클릭합니다.


## <a name="create-subscriptions-for-the-topic"></a>토픽에 대한 구독 만들기
1. 만든 **토픽**을 선택합니다.
2. **+구독**을 클릭하고, 구독 이름으로 **Subscription1**을 입력하고, 다른 모든 값은 기본값으로 유지합니다.
3. 이전 단계를 두 번 더 반복하여 **Subscription2** 및 **Subscription3**라는 구독을 만듭니다.


## <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 응용 프로그램 구성
이 샘플을 빌드하기 전에 [Java용 Azure SDK][Azure SDK for Java]를 설치했는지 확인하세요. Eclipse를 사용하는 경우 Azure SDK for Java를 포함하고 있는 [Eclipse용 Azure Toolkit][Azure Toolkit for Eclipse]를 설치할 수 있습니다. 그런 다음 **Java용 Microsoft Azure 라이브러리**를 프로젝트에 추가할 수 있습니다.

![Eclipse 빌드 경로의 라이브러리](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

또한 Java 빌드 경로에 다음 JAR을 추가해야 합니다.

- gson-2.6.2.jar
- commons-cli-1.4.jar
- proton-j-0.21.0.jar

**Main** 메서드를 사용하여 클래스를 추가한 후 Java 파일 맨 위에 다음 `import` 문을 추가합니다.

```java
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;
import static java.nio.charset.StandardCharsets.*;
import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;
import java.util.function.Function;
import org.apache.commons.cli.*;
import org.apache.commons.cli.DefaultParser;
```

## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기
**main** 메서드를 업데이트하여 **TopicClient** 개체를 만들고 Service Bus 토픽에 비동기적으로 샘플 메시지를 전송하는 도우미 메서드를 호출합니다.

> [!NOTE] 
> - `<NameOfServiceBusNamespace>`를 Service Bus 네임스페이스의 이름으로 바꿉니다. 
> - `<AccessKey>`를 네임스페이스의 액세스 키로 바꿉니다.

```java
public class MyServiceBusTopicClient {

    static final Gson GSON = new Gson();
    
    public static void main(String[] args) throws Exception, ServiceBusException {
        // TODO Auto-generated method stub

        TopicClient sendClient;
        String connectionString = "Endpoint=sb://<NameOfServiceBusNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<AccessKey>";
        sendClient = new TopicClient(new ConnectionStringBuilder(connectionString, "BasicTopic"));       
        sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());
    }

    static CompletableFuture<Void> sendMessagesAsync(TopicClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {
                        }.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("Message sending: Id = %s\n", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\tMessage acknowledged: Id = %s\n", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }
}
```

Service Bus 토픽은 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다.

## <a name="how-to-receive-messages-from-a-subscription"></a>구독에서 메시지를 받는 방법
**main** 메서드를 업데이트하여 3개 구독에 대한 3개 **SubscriptionClient** 개체를 만들고, Service Bus 토픽에서 비동기적으로 메시지를 수신하는 도우미 메서드를 호출합니다. 샘플 코드에서는 **BasicTopic**이라는 토픽과 3개의 구독 **Subscription1**, **Subscription2** 및 **Subscription3**를 만들었다고 가정합니다. 이러한 항목에 다른 이름을 사용한 경우 테스트하기 전에 코드를 업데이트합니다. 

```java
public class MyServiceBusTopicClient {

    static final Gson GSON = new Gson();
    
    public static void main(String[] args) throws Exception, ServiceBusException {
        SubscriptionClient subscription1Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription1"), ReceiveMode.PEEKLOCK);
        SubscriptionClient subscription2Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription2"), ReceiveMode.PEEKLOCK);
        SubscriptionClient subscription3Client = new SubscriptionClient(new ConnectionStringBuilder(connectionString, "BasicTopic/subscriptions/Subscription3"), ReceiveMode.PEEKLOCK);        

        registerMessageHandlerOnClient(subscription1Client);
        registerMessageHandlerOnClient(subscription2Client);
        registerMessageHandlerOnClient(subscription3Client);
    }
    
    static void registerMessageHandlerOnClient(SubscriptionClient receiveClient) throws Exception {

        // register the RegisterMessageHandler callback
        IMessageHandler messageHandler = new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                // receives message is passed to callback
                if (message.getLabel() != null &&
                        message.getContentType() != null &&
                        message.getLabel().contentEquals("Scientist") &&
                        message.getContentType().contentEquals("application/json")) {

                    byte[] body = message.getBody();
                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                    System.out.printf(
                            "\n\t\t\t\t%s Message received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            receiveClient.getEntityPath(),
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                }
                return receiveClient.completeAsync(message.getLockToken());
            }
            
            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
            }
        };

 
        receiveClient.registerMessageHandler(
                    messageHandler,
                    // callback invoked when the message handler has an exception to report
                // 1 concurrent call, messages are auto-completed, auto-renew duration
                new MessageHandlerOptions(1, false, Duration.ofMinutes(1)));

    }
}
```

## <a name="run-the-program"></a>프로그램 실행
프로그램을 실행하여 다음과 유사한 출력을 확인합니다.

```java
Message sending: Id = 0
Message sending: Id = 1
Message sending: Id = 2
Message sending: Id = 3
Message sending: Id = 4
Message sending: Id = 5
Message sending: Id = 6
Message sending: Id = 7
Message sending: Id = 8
Message sending: Id = 9
    Message acknowledged: Id = 0
    Message acknowledged: Id = 9
    Message acknowledged: Id = 7
    Message acknowledged: Id = 8
    Message acknowledged: Id = 5
    Message acknowledged: Id = 6
    Message acknowledged: Id = 3
    Message acknowledged: Id = 2
    Message acknowledged: Id = 4
    Message acknowledged: Id = 1

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 0, 
                        SequenceNumber = 11, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.442Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.442Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Albert, name = Einstein ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 9, 
                        SequenceNumber = 12, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Nikolaus, name = Kopernikus ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 8, 
                        SequenceNumber = 13, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Johannes, name = Kepler ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 7, 
                        SequenceNumber = 14, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Galileo, name = Galilei ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 6, 
                        SequenceNumber = 15, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Michael, name = Faraday ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 5, 
                        SequenceNumber = 16, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Niels, name = Bohr ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 4, 
                        SequenceNumber = 17, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Isaac, name = Newton ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription1 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 3, 
                        SequenceNumber = 18, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Steven, name = Hawking ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 2, 
                        SequenceNumber = 19, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Marie, name = Curie ]

                BasicTopic/subscriptions/Subscription2 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]

                BasicTopic/subscriptions/Subscription3 Message received: 
                        MessageId = 1, 
                        SequenceNumber = 20, 
                        EnqueuedTimeUtc = 2018-10-29T18:58:12.520Z,
                        ExpiresAtUtc = 2018-10-29T19:00:12.520Z, 
                        ContentType = "application/json",  
                        Content: [ firstName = Werner, name = Heisenberg ]
```


## <a name="next-steps"></a>다음 단계
자세한 내용은 [Service Bus 큐, 토픽 및 구독][Service Bus queues, topics, and subscriptions]을 참조하세요.

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
