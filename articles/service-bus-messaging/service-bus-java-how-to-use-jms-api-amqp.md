---
title: Java 메시지 서비스 API와 함께 AMQP를 사용 하 고 Azure Service Bus
description: Azure Service Bus 및 고급 메시지 큐 프로토콜 (AMQP) 1.0와 함께 JMS (Java Message Service)를 사용 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086694"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>Azure Service Bus 및 AMQP 1.0와 함께 Java 메시지 서비스 사용

> [!WARNING]
> 이 문서는 JMS (Java Message Service) 1.1 API에 대 한 *제한 된 지원을* 맞춘 표준 계층 Azure Service Bus에 대해서만 존재 합니다.
>
> Java 메시지 서비스 2.0 API에 대 한 완전 한 지원은 [미리 보기의 Azure Service Bus 프리미엄 계층](how-to-use-java-message-service-20.md)에서만 사용할 수 있습니다. 이 계층을 사용 하는 것이 좋습니다.
>

이 문서에서는 인기 있는 JMS API 표준을 사용 하 여 Java 응용 프로그램에서 Service Bus 메시징 기능을 사용 하는 방법을 설명 합니다. 이러한 메시징 기능에는 큐 및 토픽 게시 또는 구독이 포함 됩니다. [부록 문서](service-bus-amqp-dotnet.md) 에서는 AZURE SERVICE BUS .net API를 사용 하 여 동일한 작업을 수행 하는 방법을 설명 합니다. 이 두 문서를 함께 사용 하 여 고급 메시지 큐 프로토콜 (AMQP) 1.0를 사용 하는 플랫폼 간 메시징에 대해 알아볼 수 있습니다.

AMQP 1.0은 강력한 플랫폼 간 메시징 응용 프로그램을 빌드하는 데 사용할 수 있는 효율적이 고 신뢰할 수 있는 유선 수준 메시징 프로토콜입니다.

Service Bus에서 AMQP 1.0에 대 한 지원은 효율적인 이진 프로토콜을 사용 하 여 다양 한 플랫폼에서 큐를 사용 하 고 메시지를 게시 하거나 구독할 수 있음을 의미 합니다. 또한 다양 한 언어, 프레임 워크 및 운영 체제를 사용 하 여 빌드한 구성 요소로 구성 된 응용 프로그램을 빌드할 수 있습니다.

## <a name="get-started-with-service-bus"></a>Service Bus 시작

이 문서에서는 라는 큐가 포함 된 Service Bus 네임 스페이스가 이미 있다고 가정 `basicqueue` 합니다. 그렇지 않으면 [Azure Portal](https://portal.azure.com)를 사용 하 여 [네임 스페이스와 큐를 만들](service-bus-create-namespace-portal.md) 수 있습니다. Service Bus 네임스페이스와 큐를 만드는 방법에 대한 자세한 내용은 [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)을 참조하세요.

> [!NOTE]
> 분할된 큐 및 토픽은 또한 AMQP를 지원합니다. 자세한 내용은 [분할된 메시징 엔터티](service-bus-partitioning.md) 및 [Service Bus 분할 큐 및 토픽을 위한 AMQP 1.0 지원](./service-bus-amqp-protocol-guide.md)을 참조하세요.
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>AMQP 1.0 JMS 클라이언트 라이브러리 다운로드

Apache Qpid JMS AMQP 1.0 클라이언트 라이브러리의 최신 버전을 다운로드 하는 위치에 대 한 자세한 내용은 [Apache qpid 다운로드 사이트](https://qpid.apache.org/download.html)를 참조 하세요.

Service Bus를 사용 하 여 JMS 응용 프로그램을 빌드하고 실행할 때 Apache Qpid JMS AMQP 1.0 배포 아카이브의에서 Java CLASSPATH 환경 변수에 다음 JAR 파일을 추가 해야 합니다.

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> JMS JAR 이름 및 버전이 변경 되었을 수 있습니다. 자세한 내용은 [Qpid JMS AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)을 참조 하세요.

## <a name="code-java-applications"></a>코드 Java 응용 프로그램

### <a name="java-naming-and-directory-interface"></a>Java 명명 및 디렉터리 인터페이스

JMS는 JNDI(Java Naming and Directory Interface)를 사용하여 논리적 이름과 물리적 이름 간에 구분을 만듭니다. JNDI: **Connectionfactory** 와 **Destination**을 사용 하 여 두 가지 유형의 JMS 개체가 확인 됩니다. JNDI는 다양한 디렉터리 서비스를 연결할 수 있는 공급자 모델을 사용하여 이름 확인 책임을 처리합니다. Apache Qpid JMS AMQP 1.0 라이브러리는 다음 형식의 속성 파일을 사용 하 여 구성 된 간단한 속성 파일 기반 JNDI 공급자와 함께 제공 됩니다.

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>JNDI 컨텍스트를 설정 하 고 ConnectionFactory 개체를 구성 합니다.

참조 되는 연결 문자열은 **기본 연결 문자열**아래 [Azure Portal](https://portal.azure.com) 의 공유 액세스 정책에서 사용할 수 있는 연결 문자열입니다.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>생산자 및 소비자 대상 큐 구성

Qpid 속성 파일 JNDI 공급자에서 대상을 정의 하는 데 사용 되는 항목의 형식은 다음과 같습니다.

생산자의 대상 큐를 만들려면 다음을 수행 합니다.
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

소비자의 대상 큐를 만들려면 다음을 수행 합니다.
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>JMS 애플리케이션 작성

Service Bus와 함께 JMS를 사용 하는 경우 특별 한 Api 또는 옵션은 필요 하지 않습니다. 나중에 적용 될 몇 가지 제한 사항이 있습니다. 모든 JMS 응용 프로그램과 마찬가지로, 먼저 **Connectionfactory** 개체와 대상을 확인할 수 있도록 JNDI 환경의 구성이 필요 합니다.

#### <a name="configure-the-jndi-initialcontext-object"></a>JNDI InitialContext 개체 구성

JNDI 환경은 javax.naming.InitialContext 클래스의 생성자에 구성 정보의 해시 테이블을 전달 하 여 구성 됩니다. 해시 테이블에 있는 두 개의 필수 요소는 초기 컨텍스트 팩터리의 클래스 이름과 공급자 URL입니다. 다음 코드에서는 **servicebus**라는 속성 파일에서 qpid 속성 파일 기반 JNDI 공급자를 사용 하도록 JNDI 환경을 구성 하는 방법을 보여 줍니다.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Service Bus 큐를 사용 하는 간단한 JMS 응용 프로그램

다음 예제 프로그램에서는 JNDI 논리 이름이 큐 인 Service Bus 큐에 JMS 문자 메시지를 보내고 메시지를 다시 받습니다.

[Azure Service Bus 샘플 JMS 큐 빠른](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)시작에서 모든 소스 코드 및 구성 정보에 액세스할 수 있습니다.

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>애플리케이션 실행

공유 액세스 정책의 **연결 문자열**을 전달하여 애플리케이션을 실행합니다.
다음 출력은 응용 프로그램을 실행 하는 형식입니다.

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP 처리 및 Service Bus 작업 매핑

AMQP 처리가 Service Bus 작업으로 변환 되는 방법은 다음과 같습니다.

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS 토픽 및 Service Bus 항목

JMS API를 통해 Service Bus 토픽 및 구독을 사용 하면 기본 송신 및 수신 기능이 제공 됩니다. Jms 규격 Api를 사용 하 여 다른 메시지 브로커의 응용 프로그램을 이식 하는 경우에는 Service Bus 항목이 JMS 토픽과 다르며 몇 가지 조정이 필요 하더라도 편리 합니다.

Service Bus 항목은 Azure 리소스 관리 인터페이스, Azure 명령줄 도구 또는 Azure Portal를 통해 관리 되는 명명 된 공유 및 지속형 구독으로 메시지를 라우팅합니다. 각 구독은 최대 2000 개의 선택 규칙을 허용 합니다. 각 규칙에는 필터 조건이 있고 SQL 필터의 경우 메타 데이터 변환 동작도 있습니다. 각 필터 조건 일치는 구독으로 복사할 입력 메시지를 선택 합니다.  

구독에서 메시지를 받는 것은 큐에서 메시지를 수신 하는 것과 동일 합니다. 각 구독에는 연결 된 배달 못 한 편지 큐가 있고 메시지를 다른 큐 나 토픽으로 자동으로 전달할 수 있는 기능이 있습니다.

JMS 토픽을 사용 하면 클라이언트가 메시지 선택기를 사용 하 여 메시지를 필터링 할 수 있도록 지 속성 및 내구성 있는 구독자를 동적으로 만들 수 있습니다. 이러한 공유 되지 않은 엔터티는 Service Bus 지원 되지 않습니다. Service Bus에 대 한 SQL 필터 규칙 구문은 JMS에서 지 원하는 메시지 선택기 구문과 유사 합니다.

JMS 토픽 게시자 쪽은이 샘플에 표시 된 것 처럼 Service Bus와 호환 되지만 동적 구독자는 그렇지 않습니다. Service Bus에서 지원 되지 않는 토폴로지 관련 JMS Api는 다음과 같습니다.

## <a name="unsupported-features-and-restrictions"></a>지원되지 않는 기능 및 제한

Service Bus에서 AMQP 1.0을 통해 JMS를 사용 하는 경우 다음과 같은 제한 사항이 있습니다.

* 세션 당 **Messageproducer** 또는 **MessageConsumer** 개체는 하나만 허용 됩니다. 응용 프로그램에서 **Messageproducer** 또는 **MessageConsumer** 개체를 여러 개 만들어야 하는 경우 각각에 대 한 전용 세션을 만듭니다.
* Volatile 토픽 구독은 현재 지원 되지 않습니다.
* **Messageselector** 개체는 현재 지원 되지 않습니다.
* 분산 트랜잭션은 지원 되지 않지만 트랜잭션 된 세션은 지원 되지 않습니다.

Service Bus는 데이터 평면에서 컨트롤 평면을 분할 하므로 여러 JMS의 동적 토폴로지 함수를 지원 하지 않습니다.

| 지원되지 않는 메서드          | 다음 항목으로 교체                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | 메시지 선택기를 포트 하는 토픽 구독을 만듭니다.                                |
| createDurableConsumer       | 메시지 선택기를 포트 하는 토픽 구독을 만듭니다.                                |
| createSharedConsumer        | Service Bus 토픽은 항상 공유할 수 있습니다. "JMS 토픽 및 Service Bus 항목" 섹션을 참조 하세요.                                    |
| createSharedDurableConsumer | Service Bus 토픽은 항상 공유할 수 있습니다. "JMS 토픽 및 Service Bus 항목" 섹션을 참조 하세요.                                      |
| createTemporaryTopic        | 관리 API, 도구 또는 만료 기간으로 설정 된 *Autodeleteonidle* 으로 포털을 통해 토픽을 만듭니다. |
| createTopic                 | 관리 API, 도구 또는 포털을 통해 토픽을 만듭니다.                                         |
| unsubscribe                 | 항목 관리 API, 도구 또는 포털을 삭제 합니다.                                            |
| createBrowser               | 지원 안 됨 Service Bus API의 Peek () 기능을 사용 합니다.                         |
| createQueue                 | 관리 API, 도구 또는 포털을 통해 큐를 만듭니다.                                           | 
| createTemporaryQueue        | 관리 API, 도구 또는 만료 기간으로 설정 된 *Autodeleteonidle* 으로 포털을 통해 큐를 만듭니다. |
| receiveNoWait               | Service Bus SDK에서 제공 하는 receive () 메서드를 사용 하 고 매우 낮거나 0 시간 제한을 지정 합니다. |

## <a name="summary"></a>요약

이 문서에서는 인기 있는 JMS API 및 AMQP 1.0를 사용 하 여 Java에서 큐, 게시 또는 구독 항목과 같은 Service Bus 조정 된 메시징 기능을 사용 하는 방법을 살펴보았습니다.

.NET, C, Python, PHP 등의 다른 언어에서 Service Bus AMQP 1.0을 사용할 수도 있습니다. 이와 같은 다양 한 언어를 사용 하 여 작성 된 구성 요소는 Service Bus에서 AMQP 1.0 지원을 사용 하 여 안정적이 고 완전 한 속도로 메시지를 교환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Service Bus의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [Service Bus .NET API와 함께 AMQP 1.0 사용](./service-bus-amqp-dotnet.md)
* [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Java 개발자 센터](https://azure.microsoft.com/develop/java/)