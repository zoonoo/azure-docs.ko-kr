---
title: '빠른 시작: Kafka 프로토콜을 사용하여 Azure Event Hubs로 데이터 스트리밍'
description: '빠른 시작: 이 문서에서는 Kafka 프로토콜 및 API를 사용하여 Azure Event Hubs에 스트리밍하는 방법을 설명합니다.'
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 67ee882acab22d977f08124591289e9cfc7cded1
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261825"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>빠른 시작: Kafka 프로토콜을 사용하여 Event Hubs로 데이터 스트리밍
이 빠른 시작에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Event Hubs로 스트리밍하는 방법을 보여줍니다. 생산자와 소비자가 애플리케이션 구성을 간단하게 변경하여 Event Hubs로 대화하는 방법을 알아봅니다. Azure Event Hubs는 [Apache Kafka 버전 1.0](https://kafka.apache.org/10/documentation.html)을 지원합니다.

> [!NOTE]
> 이 샘플은 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* [Apache Kafka용 Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) 문서를 참조하세요.
* Azure 구독 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [Java Development Kit(JDK) 1.7+](https://aka.ms/azure-jdks).
* Maven 이진 아카이브를 [다운로드](https://maven.apache.org/download.cgi)하여 [설치](https://maven.apache.org/install.html)합니다.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기
**표준** 계층 Event Hubs 네임스페이스를 만들면 Kafka 엔드포인트를 네임스페이스에 사용하도록 자동으로 설정됩니다. Kafka 프로토콜을 사용하는 애플리케이션에서 표준 계층 Event Hubs로 이벤트를 스트리밍할 수 있습니다. [Azure Portal을 사용하여 이벤트 허브 만들기](event-hubs-create.md)의 단계별 지침에 따라 **표준** 계층 Event Hubs 네임스페이스를 만듭니다. 

> [!NOTE]
> Kafka용 Event Hubs는 **표준** 및 **전용** 계층에서만 사용할 수 있습니다. **기본** 계층은 Event Hubs에서 Kafka를 지원하지 않습니다.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Event Hubs에서 Kafka로 메시지 보내고 받기

1. [Kafka용 Azure Event Hubs 리포지토리](https://github.com/Azure/azure-event-hubs-for-kafka)를 복제합니다.

2. `azure-event-hubs-for-kafka/quickstart/java/producer`로 이동합니다.

3. 다음과 같이 `src/main/resources/producer.config`에서 생산자에 대한 구성 세부 정보를 업데이트합니다.

    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    GitHub에서 샘플 처리기 클래스 CustomAuthenticateCallbackHandler에 대한 소스 코드는 [여기서](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java) 찾을 수 있습니다.
4. 생산자 코드를 실행하고 이벤트를 Event Hubs로 스트리밍합니다.
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. `azure-event-hubs-for-kafka/quickstart/java/consumer`로 이동합니다.

6. 다음과 같이 `src/main/resources/consumer.config`에서 소비자에 대한 구성 세부 정보를 업데이트합니다.
   
    **TLS/SSL:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    GitHub에서 샘플 처리기 클래스 CustomAuthenticateCallbackHandler에 대한 소스 코드는 [여기서](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java) 찾을 수 있습니다.

    Kafka의 Event Hubs에 대한 모든 OAuth 샘플은 [여기서](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) 찾을 수 있습니다.
7. Kafka 클라이언트를 사용하여 소비자 코드를 실행하고 이벤트 허브에서 이벤트를 처리합니다.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Event Hubs Kafka 클러스터에 이벤트가 있는 경우 이제 소비자로부터 해당 이벤트를 수신하기 시작합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Event Hubs로 스트리밍하는 방법을 배웠습니다. 자세한 내용은 [Azure Event Hubs용 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)를 참조하세요. 
