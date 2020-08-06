---
title: Azure Service Bus 메시징-Java 메시지 서비스 엔터티 (미리 보기)
description: 이 문서에서는 Java 메시지 서비스 API를 통해 액세스할 수 있는 Azure Service Bus 메시징 엔터티에 대 한 개요를 제공 합니다.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801805"
---
# <a name="java-message-service-jms-20-entities-preview"></a>JMS (Java message service) 2.0 엔터티 (미리 보기)

Azure Service Bus Premium에 연결 하 고 [AZURE SERVICE BUS JMS 라이브러리](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) 를 활용 하는 클라이언트 응용 프로그램은 아래 엔터티를 활용할 수 있습니다.

## <a name="queues"></a>큐

JMS의 큐는 기존 [Service Bus 큐](service-bus-queues-topics-subscriptions.md#queues)와 의미상 비교할 수 있습니다.

큐를 만들려면 클래스에서 아래 메서드를 활용 합니다. `JMSContext`

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>토픽

JMS의 토픽은 기존 [Service Bus 항목과](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)의미상 비교할 수 있습니다.

항목을 만들려면 클래스에서 아래 메서드를 활용 합니다. `JMSContext`

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>임시 큐

클라이언트 응용 프로그램은 응용 프로그램의 수명 동안 존재 하는 임시 엔터티를 필요로 하는 경우 임시 큐를 사용할 수 있습니다. 이는 [요청-회신](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) 패턴에서 활용 됩니다.

임시 큐를 만들려면 클래스에서 아래 메서드를 활용 합니다. `JMSContext`

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>임시 항목

임시 큐와 마찬가지로 응용 프로그램의 수명 동안 존재 하는 임시 엔터티를 통해 게시/구독할 수 있는 임시 항목이 있습니다.

임시 토픽을 만들려면 클래스에서 아래 메서드를 활용 합니다. `JMSContext`

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>JMS (Java message service) 구독

이러한 항목은 [구독과](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 의미상 유사 하며 (즉, 항목에 존재 하 고 게시/구독 의미 **체계를 사용**하도록 설정) Java 메시지 서비스 사양에는 지정 된 구독에 대 한 공유, **공유**안 함, **내구성** 및 **비영구** 특성의 개념이 도입 되었습니다.

> [!NOTE]
> 아래 구독은 [AZURE SERVICE BUS JMS 라이브러리](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)를 사용 하 여 Azure Service Bus에 연결 하는 클라이언트 응용 프로그램용 미리 보기용 Azure Service Bus 프리미엄 계층에서 사용할 수 있습니다.
>
> 공개 미리 보기의 경우 Azure Portal를 사용 하 여 이러한 구독을 만들 수 없습니다.
>

### <a name="shared-durable-subscriptions"></a>공유 내구성이 있는 구독

응용 프로그램이 항상 구독에서 적극적으로 사용 중인지 여부에 관계 없이 항목에 게시 된 모든 메시지를 응용 프로그램에서 수신 하 고 처리 하는 경우 공유 된 내구성이 있는 구독이 사용 됩니다.

공유 구독 이므로 Service Bus에서 수신 하도록 인증 된 모든 응용 프로그램은 구독에서 받을 수 있습니다.

공유 내구성이 있는 구독을 만들려면 클래스에서 다음 메서드를 사용 합니다. `JMSContext`

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

클래스의 메서드를 사용 하 여 삭제 하지 않는 한 공유 내구성이 있는 구독은 계속 존재 합니다 `unsubscribe` `JMSContext` .

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>공유 되지 않은 내구성이 있는 구독

공유 된 내구성이 있는 구독과 마찬가지로 응용 프로그램이 항상 구독에서 적극적으로 사용 중인지 여부에 관계 없이 항목에 게시 된 모든 메시지를 응용 프로그램에서 수신 하 고 처리 하는 경우 공유 되지 않은 내구성이 있는 구독이 사용 됩니다.

그러나 공유 되지 않는 구독 이므로 구독을 만든 응용 프로그램만이 구독을 받을 수 있습니다.

공유 되지 않은 내구성이 있는 구독을 만들려면 클래스에서 다음 메서드를 사용 합니다. `JMSContext` 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`이 기능은 현재 지원 되지 않으며 무시 됩니다.
>

공유 되지 않은 영구 구독은 클래스의 메서드를 사용 하 여 삭제 하지 않는 한 계속 존재 합니다 `unsubscribe` `JMSContext` .

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>영구 공유 되지 않는 공유 구독

여러 클라이언트 응용 프로그램이 단일 구독에서 메시지를 수신 하 고 처리 해야 하는 경우에만 공유 되지 않는 공유 구독이 사용 됩니다.

구독은 지속 되지 않으므로 지속 되지 않습니다. 활성 소비자가 없는 경우에는이 구독에서 메시지를 받지 않습니다.

내구성이 없는 공유 구독을 만들려면 `JmsConsumer` 클래스에서 아래 메서드와 같이를 만듭니다. `JMSContext`

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

공유 된 내구성이 없는 구독은 활성 소비자가이를 받을 때까지 계속 존재 합니다.

### <a name="unshared-non-durable-subscriptions"></a>공유 되지 않은 내구성이 없는 구독

클라이언트 응용 프로그램이 구독에서 메시지를 수신 하 고 처리 해야 하는 경우 공유 되지 않는 비영구 구독이 사용 됩니다. 이 구독에는 소비자가 하나만 있을 수 있습니다. 즉, 구독을 만든 클라이언트입니다.

구독은 지속 되지 않으므로 지속 되지 않습니다. 활성 소비자가 없는 경우에는이 구독에서 메시지를 받지 않습니다.

공유 되지 않는 비영구 구독을 만들려면 `JMSConsumer` 클래스에서 아래 메서드와 같이를 만듭니다. `JMSContext`

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`이 기능은 현재 지원 되지 않으며 무시 됩니다.
>

공유 되지 않는 내구성이 있는 구독은 활성 소비자가 있을 때까지 계속 존재 합니다.

### <a name="message-selectors"></a>메시지 선택기

일반 Service Bus 구독에 대 한 **필터 및 작업과** 마찬가지로 JMS 구독에 대 한 **메시지 선택기** 가 존재 합니다.

메시지 선택기는 각 JMS 구독에서 설정할 수 있으며 메시지 헤더 속성에 필터 조건으로 존재 합니다. 메시지 선택기 식과 일치 하는 헤더 속성이 있는 메시지만 배달 됩니다. Null 또는 빈 문자열 값은 JMS 구독/소비자에 대 한 메시지 선택 기가 없음을 나타냅니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징을 사용하는 방법에 대한 자세한 내용 및 예제는 다음에 나오는 고급 항목을 참조하세요.

* [Service Bus 메시징 개요](service-bus-messaging-overview.md)
* [Azure Service Bus Premium (미리 보기)을 사용 하 여 Java 메시지 서비스 2.0 API 사용](how-to-use-java-message-service-20.md)



