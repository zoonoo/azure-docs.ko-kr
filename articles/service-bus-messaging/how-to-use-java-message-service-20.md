---
title: Azure Service Bus Premium과 함께 Java 메시지 서비스 2.0 API 사용
description: Azure Service Bus와 함께 JMS (Java Message Service)를 사용 하는 방법
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 894821444f74248b73578595df943cb3a0025360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698402"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>Azure Service Bus Premium과 함께 Java 메시지 서비스 2.0 API 사용

이 문서에서는 널리 사용 되는 **JMS (Java Message Service) 2.0** API를 사용 하 여 amqp 1.0 (Advanced Message queuing protocol) 프로토콜을 통해 Azure Service Bus와 상호 작용 하는 방법을 설명 합니다.

> [!NOTE]
> JMS (Java Message Service) 2.0 API에 대 한 지원은 **Azure Service Bus 프리미엄 계층** 에서만 사용할 수 있습니다.
>

## <a name="pre-requisites"></a>필수 구성 요소

### <a name="get-started-with-service-bus"></a>Service Bus 시작

이 가이드에서는 Service Bus 네임 스페이스가 이미 있다고 가정 합니다. 그렇지 않으면 [Azure Portal](https://portal.azure.com)를 사용 하 여 [네임 스페이스와 큐를 만들](service-bus-create-namespace-portal.md) 수 있습니다. 

Service Bus 네임 스페이스 및 큐를 만드는 방법에 대 한 자세한 내용은 [Azure Portal를 통해 Service Bus 큐 시작](service-bus-quickstart-portal.md)을 참조 하세요.

### <a name="set-up-a-java-development-environment"></a>Java 개발 환경 설정

Java 응용 프로그램을 개발 하려면 적절 한 개발 환경을 설정 해야 합니다. 
   * JDK (Java Development Kit) 또는 JRE (Java Runtime Environment)가 설치 되어 있어야 합니다.
   * JDK 또는 JRE는 빌드 경로와 적절 한 시스템 변수에 추가 됩니다.
   * JDK 또는 JRE를 활용 하기 위해 Java IDE 설치 됩니다. 예: Eclipse 또는 IntelliJ.

Azure에서 Java 용 개발자 환경을 준비 하는 방법에 대해 자세히 알아보려면 [이 가이드](/azure/developer/java/fundamentals/)를 활용 하세요.

## <a name="what-jms-features-are-supported"></a>지원 되는 JMS 기능은 무엇 인가요?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>JMS (Java Message Service) 클라이언트 라이브러리 다운로드

Azure Service Bus 프리미엄 계층에서 사용할 수 있는 모든 기능을 활용 하려면 아래 라이브러리를 프로젝트의 빌드 경로에 추가 해야 합니다.

[Servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> 빌드 경로에 [servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) 를 추가 하려면 프로젝트에 [Maven](https://maven.apache.org/) 또는 [Gradle](https://gradle.org/)와 같은 기본 종속성 관리 도구를 활용 합니다.
>

## <a name="coding-java-applications"></a>Java 애플리케이션 코딩

종속성을 가져온 후에는 JMS 공급자에 관계 없이 Java 응용 프로그램을 작성할 수 있습니다.

### <a name="connecting-to-azure-service-bus-using-jms"></a>JMS를 사용 하 여 Azure Service Bus에 연결

JMS 클라이언트를 사용 하 여 Azure Service Bus에 연결 하려면 **기본 연결 문자열** 아래에 있는 [Azure Portal](https://portal.azure.com) 의 ' 공유 액세스 정책 '에서 사용할 수 있는 **연결 문자열이** 필요 합니다.

1. 인스턴스화 `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. `ServiceBusJmsConnectionFactory`적절 한를 사용 하 여를 인스턴스화합니다 `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. 을 (를) 사용 하 여를 `ConnectionFactory` 만든 `Connection` 다음 `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    또는 `JMSContext` (JMS 2.0 클라이언트용)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > 이와 비슷하게 이름이 지정 된 JMS ' Session ' 및 Service Bus ' Session '은 서로 완전히 독립적입니다.
    >
    > JMS 1.1에서 Session은 MessageProducer, MessageConsumer 및 메시지 자체를 만들 수 있도록 하는 API의 필수 구성 요소입니다. 자세한 내용은 [JMS API 프로그래밍 모델](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html) 을 참조 하세요.
    >
    > Service Bus에서 [세션](message-sessions.md) 은 큐 및 구독에서 FIFO 처리를 사용 하도록 설정 하는 서비스 및 클라이언트 쪽 구문입니다.
    >

### <a name="write-the-jms-application"></a>JMS 애플리케이션 작성

또는를 `Session` `JMSContext` 인스턴스화한 후 응용 프로그램은 익숙한 JMS api를 사용 하 여 관리 및 데이터 작업을 모두 수행할 수 있습니다.

지원 되는 Api를 확인 하려면 지원 되는 [JMS 기능](how-to-use-java-message-service-20.md#what-jms-features-are-supported) 목록을 참조 하세요.

다음은 JMS를 시작 하기 위한 몇 가지 샘플 코드 조각입니다.

#### <a name="sending-messages-to-a-queue-and-topic"></a>큐 및 토픽으로 메시지 보내기

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>큐에서 메시지 받기

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>항목에 대 한 공유 지 속성 구독에서 메시지 받기

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>요약

이 가이드는 AMQP 1.0에서 JMS (Java Message Service)를 사용 하는 Java 클라이언트 응용 프로그램이 Azure Service Bus와 상호 작용할 수 있는 방법을 전시.

.NET, C, Python, PHP 등의 다른 언어에서도 Service Bus AMQP 1.0을 사용할 수 있습니다. 이러한 언어로 빌드한 구성 요소는 Service Bus의 AMQP 1.0 지원을 사용하여 안정적이며 완전히 신뢰할 수 있는 상태로 메시지를 교환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Service Bus에 대 한 자세한 내용 및 JMS (Java Message Service) 엔터티에 대 한 자세한 내용은 아래 링크를 확인 하세요. 
* [Service Bus-큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java 메시지 서비스 엔터티](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure Service Bus의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Java 메시지 서비스 API (외부 Oracle 문서)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [ActiveMQ에서 Service Bus로 마이그레이션하는 방법에 대해 알아봅니다.](migrate-jms-activemq-to-servicebus.md)