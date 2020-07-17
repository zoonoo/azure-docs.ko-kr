---
title: Java 메시지 서비스 API를 사용 하 여 AMQP & Azure Service Bus
description: Azure Service Bus 및 AMQP(Advanced Message Queuing Protocol) 1.0과 함께 JMS(Java Message Service)를 사용하는 방법을 설명합니다.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119160"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Azure Service Bus 및 AMQP 1.0와 함께 JMS (Java Message Service) 사용

Azure Service Bus에서 **고급 메시지 큐 프로토콜 (AMQP) 1.0** 프로토콜에 대 한 지원은 효율적인 이진 프로토콜을 사용 하 여 다양 한 플랫폼에서 큐 및 게시/구독 조정 된 메시징 기능을 사용할 수 있음을 의미 합니다. 뿐만 아니라 여러 언어, 프레임워크 및 운영 체제가 혼합되어 사용된 구성 요소로 이루어진 애플리케이션을 만들 수 있습니다.

이 문서에서는 AMQP 프로토콜에서 널리 사용 되는 **JMS (Java Message Service)** API를 사용 하 여 Java 응용 프로그램에서 Azure Service Bus 메시징 기능 (큐 및 게시/구독 토픽)을 사용 하는 방법을 설명 합니다.

## <a name="get-started-with-service-bus"></a>Service Bus 시작
이 가이드에서는 라는 큐를 포함 하는 Service Bus 네임 스페이스가 이미 있다고 가정 `basicqueue` 합니다. 그렇지 않으면 [Azure Portal](https://portal.azure.com)를 사용 하 여 [네임 스페이스와 큐를 만들](service-bus-create-namespace-portal.md) 수 있습니다. Service Bus 네임스페이스와 큐를 만드는 방법에 대한 자세한 내용은 [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)을 참조하세요.

> [!NOTE]
> 분할된 큐 및 토픽은 또한 AMQP를 지원합니다. 자세한 내용은 [분할된 메시징 엔터티](service-bus-partitioning.md) 및 [Service Bus 분할 큐 및 토픽을 위한 AMQP 1.0 지원](service-bus-partitioned-queues-and-topics-amqp-overview.md)을 참조하세요.
> 
>

## <a name="what-jms-features-are-supported"></a>지원 되는 JMS 기능은 무엇 인가요?

Azure Service Bus에서 지원 되는 JMS 기능은 다음과 같습니다.

| 기능 | Azure Service Bus 표준 계층-JMS 1.1 | Azure Service Bus 프리미엄 계층-JMS 2.0 (미리 보기) |
|---|---|---|
| AMQP를 통한 엔터티 자동 생성 | 지원되지 않음 | **지원됨** |
| 큐 | **지원됨** | **지원됨** |
| 토픽 | **지원됨** | **지원됨** |
| 임시 큐 | 지원되지 않음 <br/> (대신 *Autodeleteonidle* 집합을 사용 하 여 일반 큐 만들기) | **지원됨** |
| 임시 항목 | 지원되지 않음 | **지원됨** |
| 메시지 선택기 | 지원되지 않음 | **지원됨** |
| 큐 브라우저 | 지원되지 않음 <br/> (Service Bus API의 *피킹 (peeking* ) 기능 사용) | **지원됨** |
| 공유 내구성이 있는 구독 | **지원됨** | **지원됨**|
| 공유 되지 않은 내구성이 있는 구독 | 지원되지 않음 | **지원됨** |
| 영구 공유 되지 않는 공유 구독 | 지원되지 않음 | **지원됨** |
| 공유 되지 않은 내구성이 없는 구독 | 지원되지 않음 | **지원됨** |
| 영 속 구독 구독 취소 | 지원되지 않음 | **지원됨** |
| ReceiveNoWait | 지원되지 않음 | **지원됨** |
| 분산 트랜잭션 | 지원되지 않음 | 지원되지 않음 |
| 영 속 종착지 | 지원되지 않음 | 지원되지 않음 |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Service Bus 표준 계층에 대 한 추가 주의 사항
**세션**당 **Messageproducer** 또는 **MessageConsumer** 하나만 허용 됩니다. 애플리케이션에서 **MessageProducers** 또는 **MessageConsumers**를 여러 개 만들어야 하는 경우 각 항목에 대한 전용 **세션**을 만듭니다.

## <a name="downloading-the-java-message-service-jms-client-library"></a>JMS (Java Message Service) 클라이언트 라이브러리 다운로드

Azure Service Bus 연결 하 고 AMQP를 통해 JMS (Java Message Service) API를 활용 하려면 아래 라이브러리를 활용 해야 합니다. 프로젝트에 대 한 기본 종속성 관리 도구를 사용 하 여 빌드 경로에 추가 해야 합니다.

필요한 클라이언트 라이브러리는 사용 되는 가격 책정 계층에 따라 달라 집니다.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Premium 계층-AMQP를 통한 JMS 2.0 (미리 보기)

Azure Service Bus 프리미엄 계층에서 사용할 수 있는 모든 미리 보기 기능을 활용 하기 위해 [Azure servicebus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) 라이브러리를 활용 합니다.

### <a name="standard-tier---jms-11-over-amqp"></a>AMQP를 통한 표준 계층 JMS 1.1

Service Bus 표준 계층에서 지원 되는 JMS 기능을 활용 하려면 ( [지원 되는 jms 기능](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)참조) 아래 라이브러리를 활용 하세요.

* [Geronimo-jms JMS 1.1 사양](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Qpid JMS 클라이언트](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> JMS JAR 이름 및 버전이 변경되었을 수 있습니다. 자세한 내용은 [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)을 참조하세요.
>

## <a name="coding-java-applications"></a>Java 애플리케이션 코딩

종속성을 가져온 후에는 JMS 공급자에 관계 없이 Java 응용 프로그램을 작성할 수 있습니다.

Azure Service Bus Standard 및 Premium은 종속성 및 지원 되는 JMS 기능 수의 차이 때문에 두 가지 프로그래밍 모델은 약간 다릅니다.

> [!IMPORTANT]
> 아래 가이드에서는 간단한 응용 프로그램을 제공 하 여 Azure Service Bus에 연결 하는 방법을 보여 줍니다.
>
> 대부분의 엔터프라이즈 응용 프로그램 아키텍처에서 종속성 및 구성을 관리 하는 사용자 지정 방법을 사용 하는 경우 필요한 항목을 이해 하 고 응용 프로그램에 적절 하 게 적응 하려면 아래를 지침으로 사용 합니다.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>JMS를 사용 하 여 Azure Service Bus에 연결

JMS 클라이언트를 사용 하 여 Azure Service Bus에 연결 하려면 **기본 연결 문자열**아래 [Azure Portal](https://portal.azure.com) 의 ' 공유 액세스 정책 '에서 사용할 수 있는 **ConnectionString** 이 필요 합니다.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>JMS 2.0 (미리 보기)를 통해 Azure Service Bus 프리미엄에 연결

1. 인스턴스화`ServiceBusJmsConnectionFactorySettings`
    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();

    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. `ServiceBusJmsConnectionFactory`적절 한를 사용 하 여를 인스턴스화합니다 `ServiceBusConnectionString` .
    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. 을 (를) 사용 하 여를 `ConnectionFactory` 만든 `Connection` 다음`Session` 
    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    또는 `JMSContext` (JMS 2.0 클라이언트용)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>JMS 1.1을 통해 Azure Service Bus Standard에 연결

1. **Servicebus**라는 JNDI 속성 파일에 Azure Service Bus 구성을 삽입 합니다.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. JNDI 컨텍스트를 설정 하 고 ConnectionFactory를 구성 합니다.
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. 을 사용 `ConnectionFactory` 하 여을 만든 `Connection` 다음을 사용 `Session` 합니다.
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>JMS 애플리케이션 작성

또는를 `Session` `JMSContext` 인스턴스화한 후 응용 프로그램은 익숙한 JMS api를 활용 하 여 관리 및 데이터 작업을 모두 수행할 수 있습니다.

각 계층에서 지원 되는 Api를 확인 하려면 Standard 및 Premium 계층 모두에 대해 [지원 되는 JMS 기능](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) 목록을 참조 하세요.

## <a name="summary"></a>요약
이 가이드는 AMQP 1.0에서 JMS (Java Message Service)를 사용 하는 Java 클라이언트 응용 프로그램이 Azure Service Bus와 상호 작용할 수 있는 방법을 전시.

.NET, C, Python, PHP 등의 다른 언어에서도 Service Bus AMQP 1.0을 사용할 수 있습니다. 이러한 언어로 빌드한 구성 요소는 Service Bus의 AMQP 1.0 지원을 사용하여 안정적이며 완전히 신뢰할 수 있는 상태로 메시지를 교환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Service Bus에 대 한 자세한 내용 및 JMS (Java Message Service) 엔터티에 대 한 자세한 내용은 아래 링크를 확인 하세요. 
* [Service Bus-큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java 메시지 서비스 엔터티](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Azure Service Bus의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)

