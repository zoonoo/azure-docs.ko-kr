---
title: Azure Service Bus JMS 2.0 개발자 가이드
description: JMS (Java Message Service) 2.0 API를 사용 하 여 Azure Service Bus와 통신 하는 방법
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726957"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Azure Service Bus JMS 2.0 개발자 가이드

이 가이드에는 JMS (Java Message Service) 2.0 API를 사용 하 여 Azure Service Bus와 통신 하는 데 도움이 되는 자세한 정보가 포함 되어 있습니다.

Java 개발자로 서 Azure Service Bus를 처음 접하는 경우 아래 문서를 참조 하세요.

| 시작 | 개념 |
|----------------|-------|
| <ul> <li> [Azure Service Bus 정의](service-bus-messaging-overview.md) </li> <li> [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Azure Service Bus-프리미엄 계층](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>JMS (Java Message Service) 프로그래밍 모델

Java 메시지 서비스 API 프로그래밍 모델은 아래와 같습니다. 

> [!NOTE]
>
>**Azure Service Bus 프리미엄 계층** 은 jms 1.1 및 jms 2.0를 지원 합니다.
> <br> <br>
> **Azure Service Bus-표준 계층은** 제한 된 JMS 1.1 기능을 지원 합니다. 자세한 내용은이 [설명서](service-bus-java-how-to-use-jms-api-amqp.md)를 참조 하세요.
>

# <a name="jms-20-programming-model"></a>[JMS 2.0 프로그래밍 모델](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="JMS 2.0 프로그래밍 모델을 보여 주는 다이어그램." border="false":::

# <a name="jms-11-programming-model"></a>[JMS 1.1 프로그래밍 모델](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="JMS 1.1 프로그래밍 모델을 보여 주는 다이어그램." border="false":::

---

## <a name="jms---building-blocks"></a>JMS-빌딩 블록

아래 구성 요소는 JMS 응용 프로그램과 통신 하는 데 사용할 수 있습니다.

> [!NOTE]
> 아래 가이드는 [JMS (Java Message Service)에 대 한 Oracle JAVA EE 6 자습서](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html) 에서 도입 되었습니다.
>
> 이 자습서를 참조 하 여 JMS (Java Message Service)를 보다 잘 이해 하는 것이 좋습니다.
>

### <a name="connection-factory"></a>연결 팩터리
연결 팩터리 개체는 클라이언트에서 JMS 공급자와 연결 하는 데 사용 됩니다. 연결 팩터리는 관리자가 정의한 연결 구성 매개 변수 집합을 캡슐화 합니다.

각 연결 팩터리는 `ConnectionFactory` 또는 인터페이스의 인스턴스입니다 `QueueConnectionFactory` `TopicConnectionFactory` .

Azure Service Bus 연결을 간소화 하기 위해 이러한 인터페이스는 `ServiceBusJmsConnectionFactory` 각각 및를 통해 구현 됩니다 `ServiceBusJmsQueueConnectionFactory` `ServiceBusJmsTopicConnectionFactory` . 다음 매개 변수를 사용 하 여 연결 팩터리를 인스턴스화할 수 있습니다. 
   * 연결 문자열-Azure Service Bus Premium 계층 네임 스페이스에 대 한 연결 문자열입니다.
   * 다음을 포함 하는 ServiceBusJmsConnectionFactorySettings 속성 모음
      * connectionIdleTimeoutMS-유휴 연결 시간 제한 (밀리초)입니다.
      * traceFrames-디버깅을 위해 AMQP 추적 프레임을 수집 하는 부울 플래그입니다.
      * *기타 구성 매개 변수*

팩터리는 아래와 같이 만들 수 있습니다. 연결 문자열은 필수 매개 변수 이지만 추가 속성은 선택 사항입니다.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> JMS 2.0 API를 활용 하는 Java 응용 프로그램은 연결 문자열만 사용 하 여 Azure Service Bus에 연결 해야 합니다. 현재 JMS 클라이언트에 대 한 인증은 연결 문자열을 통해서만 지원 됩니다.
>
> AAD (Azure active directory) 지원 인증은 현재 지원 되지 않습니다.
>

### <a name="jms-destination"></a>JMS 대상

대상은 클라이언트에서 생성 하는 메시지의 대상과 사용 하는 메시지의 원본을 지정 하는 데 사용 하는 개체입니다.

대상은 Azure Service Bus 큐 (지점 간 시나리오) 및 토픽 (pub-sub 시나리오)에 매핑됩니다.

### <a name="connections"></a>Connections

연결은 JMS 공급자와의 가상 연결을 캡슐화 합니다. Azure Service Bus에서이는 AMQP를 통해 응용 프로그램과 Azure Service Bus 간의 상태 저장 연결을 나타냅니다.

연결은 아래와 같이 연결 팩터리에서 생성 됩니다.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>세션

세션은 메시지를 생성 하 고 소비 하는 단일 스레드 컨텍스트입니다. 메시지, 메시지 생산자 및 소비자를 만드는 데 사용할 수 있지만 트랜잭션 컨텍스트를 제공 하 여 전송 및 수신 그룹화를 작업 단위의 원자 단위로 그룹화 할 수 있습니다.

아래와 같이 연결 개체에서 세션을 만들 수 있습니다.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>세션 모드

다음 모드 중 하나를 사용 하 여 세션을 만들 수 있습니다.

| 세션 모드 | 동작 |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | 세션은 수신 호출에서 세션이 성공적으로 반환 된 경우 또는 세션에서 메시지를 처리 하기 위해 호출 하는 메시지 수신기가 성공적으로 반환 될 때 클라이언트에서 메시지를 수신 하는 것을 자동으로 승인 합니다.|
|**Session.CLIENT_ACKNOWLEDGE** |클라이언트는 메시지의 승인 메서드를 호출 하 여 사용 된 메시지를 승인 합니다.|
|**Session.DUPS_OK_ACKNOWLEDGE**|이 승인 모드는 세션에서 메시지 배달을 지연 승인 하도록 지시 합니다.| 
|**Session.SESSION_TRANSACTED**|이 값은 세션에서 로컬 트랜잭션을 사용 하도록 지정 하기 위해 Connection 개체의 메서드 createSession (int sessionMode)에 인수로 전달 될 수 있습니다.| 

세션 모드를 지정 하지 않으면 기본적으로 **Session.AUTO_ACKNOWLEDGE** 선택 됩니다.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext는 JMS 2.0 사양의 일부로 정의 됩니다.
>

JMSContext는 연결 및 세션 개체에서 제공 하는 기능을 결합 합니다. 연결 팩터리 개체에서 만들 수 있습니다.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>JMSContext 모드

**세션** 개체와 마찬가지로, [세션 모드](#session-modes)에서 설명한 것과 같은 승인 모드를 사용 하 여 jmscontext를 만들 수 있습니다.

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

모드를 지정 하지 않으면 기본적으로 **JMSContext.AUTO_ACKNOWLEDGE** 선택 됩니다.

### <a name="jms-message-producers"></a>JMS 메시지 생산자

메시지 생산자는 JMSContext 또는 세션을 사용 하 여 생성 되 고 대상으로 메시지를 보내는 데 사용 되는 개체입니다.

아래와 같이 독립 실행형 개체로 만들 수 있습니다. 

```java
JMSProducer producer = context.createProducer();
```

또는 메시지를 보내야 할 때 런타임에 생성 됩니다.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>JMS 메시지 소비자

메시지 소비자는 JMSContext 또는 세션에 의해 생성 되 고 대상으로 전송 되는 메시지를 받는 데 사용 되는 개체입니다. 아래와 같이 만들 수 있습니다.

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Receive () 메서드를 통한 동기 수신

메시지 소비자는 메서드를 통해 대상에서 메시지를 수신 하는 동기식 방법을 제공 합니다 `receive()` .

인수/시간 제한이 지정 되지 않았거나 제한 시간 ' 0 '이 지정 된 경우 메시지가 도착 하지 않으면 소비자가 무기한 차단 되 고, 그렇지 않으면 연결이 끊어집니다.

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

0이 아닌 긍정 인수를 제공 하면 타이머가 만료 될 때까지 소비자가 차단 됩니다.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>JMS 메시지 수신기를 사용 하 여 비동기 수신

메시지 수신기는 대상에서 메시지를 비동기적으로 처리 하는 데 사용 되는 개체입니다. `MessageListener` `onMessage` 특정 비즈니스 논리가 라이브 되어야 하는 메서드가 포함 된 인터페이스를 구현 합니다.

메시지 수신기 개체를 인스턴스화하고 메서드를 사용 하 여 특정 메시지 소비자에 대해 등록 해야 합니다 `setMessageListener` .

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>항목에서 사용

[JMS 메시지 소비자](#jms-message-consumers) 는 큐 또는 토픽 일 수 있는 [대상](#jms-destination) 에 대해 만들어집니다.

큐의 소비자는 클라이언트 응용 프로그램과 Azure Service Bus 간에 세션 (및 연결)의 컨텍스트에 상주 하는 클라이언트 쪽 개체 뿐입니다.

그러나 항목의 소비자는 2 부분으로 구성 됩니다. 
   * 세션 (또는 JMSContext)의 컨텍스트에 상주 하는 **클라이언트 쪽 개체** 및
   * Azure Service Bus의 엔터티인 **구독** 입니다.

구독은 [여기](java-message-service-20-entities.md#java-message-service-jms-subscriptions) 에 설명 되어 있으며 다음 중 하나일 수 있습니다. 
   * 공유 내구성이 있는 구독
   * 영구 공유 되지 않는 공유 구독
   * 공유 되지 않은 내구성이 있는 구독
   * 공유 되지 않은 내구성이 없는 구독

### <a name="jms-queue-browsers"></a>JMS 큐 브라우저

JMS API는 `QueueBrowser` 응용 프로그램이 큐의 메시지를 찾아보고 각 메시지의 헤더 값을 표시할 수 있도록 하는 개체를 제공 합니다.

아래와 같이 JMSContext를 사용 하 여 큐 브라우저를 만들 수 있습니다.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> JMS API는 토픽을 찾아보기 위한 API를 제공 하지 않습니다.
>
> 토픽 자체가 메시지를 저장 하지 않기 때문입니다. 항목이 토픽으로 전송 되는 즉시 해당 구독으로 전달 됩니다.
>

### <a name="jms-message-selectors"></a>JMS 메시지 선택기

메시지 선택기는 수신 응용 프로그램에서 수신 된 메시지를 필터링 하는 데 사용할 수 있습니다. 메시지 선택기를 사용 하 여 수신 응용 프로그램은 자체 역할을 수행 하는 대신 JMS 공급자 (이 경우 Azure Service Bus)로 메시지 필터링 작업을 오프 로드 합니다.

다음 소비자 중 하나를 만들 때 선택기를 사용할 수 있습니다. 
   * 공유 내구성이 있는 구독
   * 공유 되지 않은 영구 구독
   * 영구 공유 되지 않는 공유 구독
   * 공유 되지 않은 내구성이 없는 구독
   * 큐 브라우저


## <a name="summary"></a>요약

이 개발자 가이드에서는 JMS (Java Message Service)를 사용 하는 Java 클라이언트 응용 프로그램이 Azure Service Bus에 연결할 수 있는 방법을 전시.

## <a name="next-steps"></a>다음 단계

Azure Service Bus에 대 한 자세한 내용 및 JMS (Java Message Service) 엔터티에 대 한 자세한 내용은 아래 링크를 확인 하세요. 
* [Service Bus-큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java 메시지 서비스 엔터티](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Azure Service Bus의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Java 메시지 서비스 API (외부 Oracle 문서)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [ActiveMQ에서 Service Bus로 마이그레이션하는 방법에 대해 알아봅니다.](migrate-jms-activemq-to-servicebus.md)
