---
title: ActiveMQ에서 Azure Service Bus로 JMS (Java Message Service) 응용 프로그램 마이그레이션 Microsoft Docs
description: 이 문서에서는 활성 MQ와 상호 작용 하 여 Azure Service Bus와 상호 작용 하는 기존 JMS 응용 프로그램을 마이그레이션하는 방법을 설명 합니다.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122338"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>활성 MQ에서 기존 JMS (Java Message Service) 2.0 응용 프로그램을 Azure Service Bus로 마이그레이션

Azure Service Bus는 AMQP (Advanced Message Queuing Protocol) 프로토콜을 통해 JMS (Java Message Service) 2.0 API를 활용 하는 Java/J2EE 및 스프링 워크 로드를 지원 합니다.

이 가이드에서는 Azure Service Bus와 상호 작용 하기 위해 JMS Broker (특히 Apache ActiveMQ 또는 Amazon MQ)와 상호 작용 하는 기존 JMS (Java Message Service) 2.0 응용 프로그램을 수정 하려는 경우 알아두어야 하는 사항에 대해 설명 합니다.

## <a name="before-you-start"></a>시작하기 전에

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure Service Bus와 Apache ActiveMQ의 차이점

Azure Service Bus와 Apache ActiveMQ는 모두 클라이언트 응용 프로그램에서 메시지를 보내고 받을 수 있도록 JMS 공급자 역할을 하는 메시지 브로커입니다. **항목** 및 **구독과**함께 **큐** 및 게시-구독 의미 체계를 사용 하 여 지점-지점 의미 체계를 사용 하도록 설정 합니다. 

이 두 가지 경우에도 몇 가지 차이점이 있습니다.

| 범주 | 활성 MQ | Azure Service Bus |
| --- | --- | --- |
| 응용 프로그램 계층화 | 클러스터 된 모놀리식 | 2 계층 <br> (게이트웨이 + 백 엔드) |
| 프로토콜 지원 | <ul> <li>AMQP</li> <li> 무시할 </li> <li> OpenWire </li> </ul> | AMQP |
| 프로비저닝 모드 | <ul> <li> IaaS (온-프레미스) </li> <li> Amazon MQ (관리 되는 PaaS) </li> | 관리 되는 PaaS |
| 메시지 크기 | 고객 구성 가능 | 1mb (프리미엄 계층) |
| 고가용성 | 고객 관리 | 플랫폼 관리 |
| 재해 복구 | 고객 관리 | 플랫폼 관리 | 

### <a name="current-supported-and-unsupported-features"></a>현재 지원 되는 기능 및 지원 되지 않는 기능

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>제한 사항

Azure Service Bus의 2 계층 특성은 다양 한 비즈니스 연속성 기능 (고가용성 및 재해 복구)을 제공 합니다. 그러나 JMS 기능을 활용할 때 몇 가지 고려 사항이 있습니다.

#### <a name="service-upgrades"></a>서비스 업그레이드

Service bus 업그레이드 및 다시 시작의 경우 임시 큐 또는 항목이 삭제 됩니다.

응용 프로그램이 임시 큐 또는 토픽에서 데이터 손실이 발생 하는 경우 임시 큐 또는 토픽을 사용 **하지** 말고 지속형 큐, 토픽 및 구독을 대신 사용 하는 것이 좋습니다.

#### <a name="data-migration"></a>데이터 마이그레이션

Azure Service Bus와 상호 작용 하도록 클라이언트 응용 프로그램을 마이그레이션/수정 하는 과정에서 ActiveMQ에 저장 된 데이터는 Service Bus으로 마이그레이션되지 않습니다.

사용자 지정 응용 프로그램은 ActiveMQ 큐, 토픽 및 구독을 드레이닝 하 고 Service Bus 큐, 토픽 및 구독에 대 한 메시지를 재생 하는 데 필요할 수 있습니다.

#### <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure ActiveDirectory에 의해 지원 되는 RBAC (역할 기반 Access Control)는 Azure Service Bus에 대 한 기본 인증 메커니즘입니다.

그러나 RBAC는 Apache QPID JMS에서 클레임 기반 인증을 지원 하지 않기 때문에 현재 지원 되지 않습니다.

지금은 인증은 SAS 키로만 지원 됩니다.

## <a name="pre-migration"></a>사전 마이그레이션

### <a name="version-check"></a>버전 확인

다음은 JMS 응용 프로그램을 작성 하는 동안 사용 된 구성 요소 및 지원 되는 특정 버전입니다. 

| 구성 요소 | 버전 |
|---|---|
| JMS (Java Message Service) API | 1.1 이상 |
| AMQP 프로토콜 | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>AMQP 포트가 열려 있는지 확인 합니다.

Azure Service Bus는 AMQP 프로토콜을 통한 통신을 지원 합니다. 이러한 목적을 위해 포트 5671 (AMQP) 및 443 (TCP)을 통한 통신을 사용 하도록 설정 해야 합니다. 클라이언트 응용 프로그램이 호스팅되는 위치에 따라 이러한 포트를 통한 통신을 허용 하는 지원 티켓이 필요할 수 있습니다.

> [!IMPORTANT]
> Azure Service Bus는 **amqp** 1.0 프로토콜만 지원 합니다.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>엔터프라이즈 구성 (VNET, 방화벽, 개인 끝점 등) 설정

Azure Service Bus를 통해 다양 한 엔터프라이즈 보안 및 고가용성 기능을 사용할 수 있습니다. 자세히 알아보려면 아래 설명서 링크를 따르세요.

  * [Virtual Network 서비스 끝점](service-bus-service-endpoints.md)
  * [방화벽](service-bus-ip-filtering.md)
  * [BYOK (고객 관리 키)를 사용 하는 서비스 쪽 암호화](configure-customer-managed-key.md)
  * [프라이빗 엔드포인트](private-link-service.md)
  * [인증 및 권한 부여](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>모니터링, 경고 및 추적

메트릭은 각 Service Bus 네임 스페이스에 대해 Azure Monitor에 게시 되 고 네임 스페이스에 할당 된 리소스의 경고 및 동적 크기 조정에 활용할 수 있습니다.

다른 메트릭과 [Azure Monitor에서 Service Bus 메트릭에](service-bus-metrics-azure-monitor.md)대해 경고를 설정 하는 방법에 대해 자세히 알아보세요.

또한 [데이터 작업을 위한 클라이언트 쪽 추적](service-bus-end-to-end-tracing.md) 및 [관리 작업에 대 한 운영/진단 로깅](service-bus-diagnostic-logs.md) 에 대 한 자세한 내용을 참조 하세요.

### <a name="metrics---newrelic"></a>메트릭-NewRelic

다음은 Azure Service Bus의 메트릭에 대 한 ActiveMQ 맵의 메트릭에 대 한 유용한 지침입니다. 다음은 NewRelic에서 참조 됩니다.

  * [ActiveMQ/Amazon MQ NewRelic 메트릭](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure Service Bus NewRelic 메트릭](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> 현재 NewRelic은 ActiveMQ와 직접 통합 되지 않지만 Amazon MQ에 사용할 수 있는 메트릭이 있습니다.
> Amazon MQ는 ActiveMQ에서 파생 되므로 아래 가이드는 AmazonMQ의 NewRelic 메트릭을 Azure Service Bus에 매핑합니다.
>

|메트릭 그룹화| AmazonMQ/활성 MQ 메트릭 | Azure Service Bus 메트릭 |
|------------|---------------------------|--------------------------|
|Broker|`CpuUtilization`|`CPUXNS`|
|Broker|`MemoryUsage`|`WSXNS`|
|Broker|`CurrentConnectionsCount`|`activeConnections`|
|Broker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Broker|`InactiveDurableTopicSubscribersCount`|구독 메트릭 활용|
|Broker|`TotalMessageCount`|큐/토픽/구독 수준 활용`activeMessages`|
|큐/토픽|`EnqueueCount`|`incomingMessages`|
|큐/토픽|`DequeueCount`|`outgoingMessages`|
|큐|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>마이그레이션

Azure Service Bus와 상호 작용 하도록 기존 JMS 2.0 응용 프로그램을 마이그레이션하려면 아래 단계를 수행 해야 합니다.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>ActiveMQ에서 토폴로지를 내보내고 Azure Service Bus에서 엔터티를 만듭니다 (선택 사항).

클라이언트 응용 프로그램이 Azure Service Bus와 원활 하 게 연결할 수 있도록 하기 위해, 토폴로지 (큐, 토픽 및 구독 포함)는 **Apache ActiveMQ** 에서 **Azure Service Bus**로 마이그레이션해야 합니다.

> [!NOTE]
> JMS (Java Message Service) 응용 프로그램의 경우 큐, 토픽 및 구독을 만드는 작업은 런타임 작업입니다. 대부분의 JMS (Java Message Service) 공급자 (메시지 브로커)는 런타임에 *큐*, *토픽* 및 *구독* 을 만드는 기능을 제공 합니다.
>
> 따라서 위의 단계는 선택 사항입니다.
>
> 런타임에 토폴로지를 만들 수 있는 권한이 응용 프로그램에 있는지 확인 하려면 ***SAS ' 관리 '*** 권한이 있는 연결 문자열이 사용 되는지 확인 하세요.

원하는 작업 
  * [Activemq 명령줄 도구](https://activemq.apache.org/activemq-command-line-tools-reference) 를 활용 하 여 토폴로지 내보내기
  * [Azure Resource Manager 템플릿을](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 사용 하 여 동일한 토폴로지 다시 만들기
  * Azure Resource Manager 템플릿을 실행 합니다.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Service Bus JMS 구현에 대 한 maven 종속성 가져오기

Azure Service Bus와 원활 하 게 연결 되도록 하려면 다음과 같이 Maven 파일에 대 한 종속성으로 ***Azure servicebus*** 패키지를 추가 해야 합니다 `pom.xml` .

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>응용 프로그램 서버 구성 변경

이 부분은 활성 MQ에 연결 하는 클라이언트 응용 프로그램을 호스트 하는 응용 프로그램 서버에 대 한 사용자 지정입니다.

#### <a name="tomcat"></a>Tomcat

여기서는 파일에 표시 된 것 처럼 활성 MQ와 관련 된 구성으로 시작 `/META-INF/context.xml` 합니다.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

Azure Service Bus를 가리키도록 아래와 같이 적용할 수 있습니다.

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>스프링 응용 프로그램

##### <a name="update-applicationproperties-file"></a>업데이트 `application.properties` 파일

스프링 부팅 응용 프로그램을 사용 하 여 ActiveMQ에 연결 하는 경우

여기서의 목표는 파일에서 ActiveMQ 특정 속성을 ***제거*** 하는 것입니다 `application.properties` .

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

그런 다음 파일에 Service Bus 특정 속성을 ***추가*** `application.properties` 합니다.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>ActiveMQConnectionFactory를 ServiceBusJmsConnectionFactory로 바꿉니다.

다음 단계는 ActiveMQConnectionFactory의 인스턴스를 ServiceBusJmsConnectionFactory로 바꾸는 것입니다.

> [!NOTE] 
> 실제 코드 변경은 응용 프로그램에 따라 달라 지 고 종속성을 관리 하는 방법 ***에 대 한*** 지침을 제공 합니다.
>

이전에는 아래와 같이 ActiveMQConnectionFactory의 개체를 인스턴스화할 수 있습니다.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

이는 ServiceBusJmsConnectionFactory의 개체 인스턴스화로 변경 됩니다.

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>마이그레이션 후 작업

이제 응용 프로그램을 수정 하 여 Azure Service Bus에서 메시지를 보내고 받을 수 있도록 했으므로 응용 프로그램이 정상적으로 작동 하는지 확인 해야 합니다. 이 작업이 완료 되 면 계속 해 서 응용 프로그램 스택을 구체화 하 고 현대화 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Service Bus와의 원활한 통합을 위해 [AZURE SERVICE BUS JMS 용 스프링 부팅 스타터](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) 를 활용 하세요.

Service Bus 메시징 및 JMS (Java Message Service)에 대해 자세히 알아보려면 다음 항목을 참조 하세요.

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
