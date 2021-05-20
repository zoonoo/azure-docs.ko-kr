---
title: Azure Service Bus JMS 2.0 개발자 가이드
description: JMS(Java Message Service) 2.0 API를 사용하여 Azure Service Bus와 통신하는 방법
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726957"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Azure Service Bus JMS 2.0 개발자 가이드

이 가이드에는 JMS(Java Message Service) 2.0 API를 사용하여 Azure Service Bus와 통신하는 데 도움이 되는 자세한 정보가 포함되어 있습니다.

Azure Service Bus를 처음 접하는 Java 개발자는 아래 문서를 참조하세요.

| 시작 | 개념 |
|----------------|-------|
| <ul> <li> [Azure Service Bus란?](service-bus-messaging-overview.md) </li> <li> [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus - 프리미엄 계층](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>JMS(Java Message Service) 프로그래밍 모델

Java Message Service API 프로그래밍 모델은 아래와 같습니다. 

> [!NOTE]
>
>**Azure Service Bus 프리미엄 계층** 은 JMS 1.1 및 JMS 2.0을 지원합니다.
> <br> <br>
> **Azure Service Bus - 표준** 계층은 제한된 JMS 1.1 기능을 지원합니다. 자세한 내용은 이 [설명서](service-bus-java-how-to-use-jms-api-amqp.md)를 참조하세요.
>

# <a name="jms-20-programming-model"></a>[JMS 2.0 프로그래밍 모델](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="JMS 2.0 프로그래밍 모델을 보여 주는 다이어그램." border="false":::

# <a name="jms-11-programming-model"></a>[JMS 1.1 프로그래밍 모델](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="JMS 1.1 프로그래밍 모델을 보여 주는 다이어그램." border="false":::

---

## <a name="jms---building-blocks"></a>JMS - 빌딩 블록

아래 빌딩 블록은 JMS 애플리케이션과 통신하는 데 사용할 수 있습니다.

> [!NOTE]
> 아래 가이드는 [JMS(Java Message Service)에 대한 Oracle Java EE 6 자습서](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)를 각색한 것입니다.
>
> JMS(Java Message Service)를 보다 잘 이해하려면 이 자습서를 참조하는 것이 좋습니다.
>

### <a name="connection-factory"></a>연결 팩터리
연결 팩터리 개체는 클라이언트에서 JMS 공급자와 연결하는 데 사용됩니다. 연결 팩터리는 관리자가 정의한 연결 구성 매개 변수 집합을 캡슐화합니다.

각 연결 팩터리는 `ConnectionFactory`, `QueueConnectionFactory` 또는 `TopicConnectionFactory` 인터페이스의 인스턴스입니다.

Azure Service Bus와의 연결을 간소화하기 위해 이러한 인터페이스는 각각 `ServiceBusJmsConnectionFactory`, `ServiceBusJmsQueueConnectionFactory` 및 `ServiceBusJmsTopicConnectionFactory`를 통해 구현됩니다. 다음 매개 변수를 사용하여 연결 팩터리를 인스턴스화할 수 있습니다. 
   * 연결 문자열 - Azure Service Bus 프리미엄 계층 네임스페이스의 연결 문자열입니다.
   * 다음을 포함하는 ServiceBusJmsConnectionFactorySettings 속성 모음
      * connectionIdleTimeoutMS - 유휴 연결 제한 시간(밀리초)입니다.
      * traceFrames - 디버깅을 위해 AMQP 추적 프레임을 수집하는 부울 플래그입니다.
      * *기타 구성 매개 변수*

팩터리는 아래와 같이 만들 수 있습니다. 연결 문자열은 필수 매개 변수이지만 추가 속성은 선택 사항입니다.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> JMS 2.0 API를 활용하는 Java 애플리케이션은 연결 문자열만 사용하여 Azure Service Bus에 연결해야 합니다. 현재 JMS 클라이언트에 대한 인증은 연결 문자열을 통해서만 지원됩니다.
>
> AAD(Azure Active Directory) 지원 인증은 현재 지원되지 않습니다.
>

### <a name="jms-destination"></a>JMS 대상

대상은 클라이언트에서 생성하는 메시지의 대상과 클라이언트에서 소비하는 메시지의 원본을 지정하는 데 사용하는 개체입니다.

대상은 Azure Service Bus - 큐(지점 간 시나리오) 및 토픽(게시-구독 시나리오)의 엔터티에 매핑됩니다.

### <a name="connections"></a>Connections

연결은 JMS 공급자와의 가상 연결을 캡슐화합니다. Azure Service Bus에서 이는 AMQP를 통해 애플리케이션과 Azure Service Bus 간의 상태 저장 연결을 나타냅니다.

연결은 아래와 같이 연결 팩터리에서 생성됩니다.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>세션

세션은 메시지를 생성하고 소비하기 위한 단일 스레드 컨텍스트입니다. 메시지, 메시지 생산자 및 소비자를 만드는 데 사용할 수 있지만, 전송 및 수신을 작업의 원자 단위로 그룹화하도록 허용하는 트랜잭션 컨텍스트도 제공합니다.

아래와 같이 연결 개체로 세션을 만들 수 있습니다.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>세션 모드

다음 모드 중 하나를 사용하여 세션을 만들 수 있습니다.

| 세션 모드 | 동작 |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | 수신 호출에서 세션이 성공적으로 반환된 경우 또는 세션에서 메시지를 처리하기 위해 호출한 메시지 수신기가 성공적으로 반환될 경우, 세션은 클라이언트의 메시지 수신을 자동으로 승인합니다.|
|**Session.CLIENT_ACKNOWLEDGE** |클라이언트는 메시지의 승인 방법을 호출하여 소비된 메시지를 승인합니다.|
|**Session.DUPS_OK_ACKNOWLEDGE**|이 승인 모드는 메시지 배달을 지연 승인하도록 세션에 지시합니다.| 
|**Session.SESSION_TRANSACTED**|이 값은 세션에서 로컬 트랜잭션을 사용하도록 지정하기 위해 Connection 개체의 createSession(int sessionMode) 메서드에 인수로 전달될 수 있습니다.| 

세션 모드를 지정하지 않으면 기본적으로 **Session.AUTO_ACKNOWLEDGE** 가 선택됩니다.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext는 JMS 2.0 사양의 일부로 정의됩니다.
>

JMSContext는 연결 및 세션 개체에서 제공하는 기능을 결합합니다. 연결 팩터리 개체에서 만들 수 있습니다.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext 모드

**세션** 개체와 마찬가지로, [세션 모드](#session-modes)에서 설명한 것과 같은 승인 모드를 사용하여 JMSContext를 만들 수 있습니다.

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

모드를 지정하지 않으면 기본적으로 **JMSContext.AUTO_ACKNOWLEDGE** 가 선택됩니다.

### <a name="jms-message-producers"></a>JMS 메시지 생산자

메시지 생산자는 JMSContext 또는 Session을 사용하여 생성되고 대상으로 메시지를 보내는 데 사용되는 개체입니다.

아래와 같이 독립 실행형 개체로 생성될 수 있습니다. 

```java
JMSProducer producer = context.createProducer();
```

또는 런타임에 메시지를 보내야 할 때 생성될 수 있습니다.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS 메시지 소비자

메시지 소비자는 JMSContext 또는 세션에 의해 생성되고 대상으로 전송되는 메시지를 수신하는 데 사용되는 개체입니다. 아래와 같이 만들 수 있습니다.

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>receive() 메서드를 통한 동기 수신

메시지 소비자는 `receive()` 메서드를 통해 대상에서 메시지를 수신하는 동기 방법을 제공합니다.

인수/제한 시간이 지정되지 않았거나 제한 시간 '0'이 지정된 경우에 메시지가 도착하거나 연결이 끊어질 때까지(둘 중 빠른 쪽) 소비자가 무기한으로 차단됩니다.

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

0이 아닌 긍정 인수를 제공하면 타이머가 만료될 때까지 소비자가 차단됩니다.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>JMS 메시지 수신기를 사용한 비동기 수신

메시지 수신기는 대상에서 메시지를 비동기적으로 처리하는 데 사용되는 개체입니다. 특정 비즈니스 논리가 상주해야 하는 `onMessage` 메서드가 포함된 `MessageListener` 인터페이스를 구현합니다.

메시지 수신기 개체를 인스턴스화하고 `setMessageListener` 메서드를 사용하여 특정 메시지 소비자에 등록해야 합니다.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>토픽에서 소비

큐 또는 토픽일 수 있는 [대상](#jms-destination)에 대해 [JMS 메시지 소비자](#jms-message-consumers)가 생성됩니다.

큐의 소비자는 클라이언트 애플리케이션과 Azure Service Bus 간 Session(및 Connection)의 컨텍스트에 상주하는 클라이언트 쪽 개체일 뿐입니다.

그러나 토픽의 소비자는 2개 부분으로 구성됩니다. 
   * Session(또는 JMSContext)의 컨텍스트에 상주하는 **클라이언트 쪽 개체** 및
   * Azure Service Bus의 엔터티인 **구독**.

구독은 [여기](java-message-service-20-entities.md#java-message-service-jms-subscriptions)에 설명되어 있으며 다음 중 하나일 수 있습니다. 
   * 공유되는 지속형 구독
   * 공유되는 비지속형 구독
   * 공유되지 않는 지속형 구독
   * 공유되지 않는 비지속형 구독

### <a name="jms-queue-browsers"></a>JMS 큐 브라우저

JMS API는 애플리케이션이 큐의 메시지를 찾아보고 각 메시지의 헤더 값을 표시할 수 있도록 하는 `QueueBrowser` 개체를 제공합니다.

아래와 같이 JMSContext를 사용하여 큐 브라우저를 만들 수 있습니다.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> JMS API는 토픽을 찾아보기 위한 API를 제공 하지 않습니다.
>
> 토픽 자체가 메시지를 저장하지 않기 때문입니다. 메시지가 토픽으로 전송되는 즉시 해당 구독으로 전달됩니다.
>

### <a name="jms-message-selectors"></a>JMS 메시지 선택기

메시지 선택기는 수신 애플리케이션에서 수신된 메시지를 필터링하는 데 사용할 수 있습니다. 수신 애플리케이션은 메시지 선택기를 사용하여 메시지 필터링 작업을 직접 처리하지 않고 JMS 공급자(이 경우 Azure Service Bus)로 오프로드합니다.

다음 소비자 중 하나를 만들 때 선택기를 활용할 수 있습니다. 
   * 공유되는 지속형 구독
   * 공유되지 않는 지속형 구독
   * 공유되는 비지속형 구독
   * 공유되지 않는 비지속형 구독
   * 큐 브라우저


## <a name="summary"></a>요약

본 개발자 가이드에서는 JMS(Java Message Service)를 이용하는 Java 클라이언트 애플리케이션이 Azure Service Bus와 상호 작용하는 방법을 보여 주었습니다.

## <a name="next-steps"></a>다음 단계

Azure Service Bus에 대한 자세한 내용과 JMS(Java Message Service) 엔터티 관련 세부 정보는 다음 링크를 확인하세요. 
* [Service Bus - 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus - Java Message Service 엔터티](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure Service Bus의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service API(외부 Oracle 문서)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [ActiveMQ에서 Service Bus로의 마이그레이션 방법 알아보기](migrate-jms-activemq-to-servicebus.md)
