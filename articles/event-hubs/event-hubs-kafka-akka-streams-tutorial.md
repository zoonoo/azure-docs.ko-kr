---
title: Apache Kafka용 Azure Event Hubs에서 Akka Streams 사용 | Microsoft Docs
description: Akka Streams를 Kafka 지원 이벤트 허브에 연결합니다.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: f6671e05c53b8be2e06eb969adc8a4d5736aff37
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284454"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Apache Kafka용 Event Hubs에서 Akka Streams 사용

Apache Kafka를 사용하는 주요 이점 중 하나는 연결할 수 있는 프레임워크의 에코시스템입니다. Kafka 지원 Event Hubs는 Kafka의 유연성과 Azure 에코시스템의 확장성, 일관성 및 지원을 결합합니다.

이 자습서에서는 프로토콜 클라이언트를 변경하거나 사용자 고유의 클러스터를 실행하지 않고 Akka Streams를 Kafka 지원 이벤트 허브에 연결하는 방법을 보여 줍니다. Apache Kafka용 Azure Event Hubs는 [Apache Kafka 버전 1.0](https://kafka.apache.org/10/documentation.html)을 지원합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* Azure 구독. 구독이 없으면 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
* [JDK(Java Development Kit) 1.8 이상](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Ubuntu에서 `apt-get install default-jdk`를 실행하여 JDK를 설치합니다.
    * JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.
* Maven 이진 보관 파일을 [다운로드](http://maven.apache.org/download.cgi)하여 [설치](http://maven.apache.org/install.html)합니다.
    * Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu에서 `sudo apt-get install git`를 실행하여 Git를 실행할 수 있습니다.

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

Event Hubs 서비스에서 보내거나 받으려면 Event Hubs 네임스페이스가 필요합니다. Event Hubs Kafka 엔드포인트를 가져오는 방법에 대한 내용은 [Kafka 지원 Event Hubs 만들기](event-hubs-create-kafka-enabled.md)를 참조하세요. 나중에 사용하기 위해 Event Hubs 연결 문자열을 복사합니다.

## <a name="clone-the-example-project"></a>프로젝트 예제 복제

이제 Kafka 지원 Event Hubs 연결 문자열이 있으므로 Azure Event Hubs 리포지토리를 복제하고 `akka` 하위 폴더로 이동합니다.

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Akka Streams 생산자

제공된 Akka Streams 생산자 예제를 사용하여 Event Hubs 서비스에 메시지를 보냅니다.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka 엔드포인트 제공

#### <a name="producer-applicationconf"></a>생산자 application.conf

생산자에서 Event Hubs Kafka 엔드포인트를 가리키도록 `producer/src/main/resources/application.conf`에서 `bootstrap.servers` 및 `sasl.jaas.config` 값을 업데이트합니다.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>명령줄에서 생성자 실행

명령줄에서 생성자를 실행하려면 JAR을 생성한 다음, Maven 내에서 실행하거나, Maven을 사용하여 JAR을 생성한 다음, 필요한 Kafka JAR을 클래스 경로에 추가하여 Java에서 실행합니다.

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

생산자에서 `test` 항목의 Kafka 지원 이벤트 허브에 이벤트를 보내기 시작하고 해당 이벤트를 표준 출력으로 출력합니다.

## <a name="akka-streams-consumer"></a>Akka Streams 소비자

제공된 소비자 예제를 사용하여 Kafka 지원 이벤트 허브로부터 메시지를 받습니다.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Event Hubs Kafka 엔드포인트 제공

#### <a name="consumer-applicationconf"></a>소비자 application.conf

소비자에서 Event Hubs Kafka 엔드포인트를 가리키도록 `consumer/src/main/resources/application.conf`에서 `bootstrap.servers` 및 `sasl.jaas.config` 값을 업데이트합니다.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>명령줄에서 소비자 실행

명령줄에서 소비자를 실행하려면 JAR을 생성하고 Maven 내에서 실행하거나, Maven을 사용하여 JAR을 생성하고 필요한 Kafka JAR을 클래스 경로에 추가하여 Java에서 실행합니다.

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Kafka 지원 이벤트 허브에 이벤트가 있는 경우(예: 생산자도 실행 중인 경우), 소비자에서 `test` 항목으로부터 이벤트를 받기 시작합니다. 

Akka Streams에 대한 자세한 내용은 [Akka Streams Kafka 가이드](https://doc.akka.io/docs/akka-stream-kafka/current/home.html)를 확인하세요.

## <a name="next-steps"></a>다음 단계

* [Event Hubs에 대해 알아봅니다](event-hubs-what-is-event-hubs.md).
* [Kafka용 Event Hubs에 대해 알아보기](event-hubs-for-kafka-ecosystem-overview.md)
* [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)를 사용하여 [온-프레미스 Kafka에서 클라우드의 Kafka 지원 Event Hubs로 이벤트를 스트림합니다.](event-hubs-kafka-mirror-maker-tutorial.md)
* [네이티브 Kafka 응용 프로그램](event-hubs-quickstart-kafka-enabled-event-hubs.md) 또는 [Apache Flink](event-hubs-kafka-flink-tutorial.md)를 사용하여 Kafka 지원 Event Hubs에 스트림하는 방법을 알아봅니다.
