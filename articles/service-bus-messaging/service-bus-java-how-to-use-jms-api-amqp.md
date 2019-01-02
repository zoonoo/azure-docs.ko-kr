---
title: Java Service Bus API와 함께 AMQP 1.0을 사용하는 방법 | Microsoft Docs
description: Azure Service Bus 및 AMQP(Advanced Message Queuing Protocol) 1.0과 함께 JMS(Java Message Service)를 사용하는 방법을 설명합니다.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2018
ms.author: spelluru
ms.openlocfilehash: fbd74b227afd2191616100d74c7864eacf015add
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308073"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Service Bus 및 AMQP 1.0과 함께 JMS(Java Message Service) API를 사용하는 방법
AMQP(Advanced Message Queuing Protocol) 1.0은 강력한 크로스 플랫폼 메시징 애플리케이션을 빌드하는 데 사용할 수 있는 효율성과 안정성이 뛰어난 유선 수준 메시징 프로토콜입니다.

Service Bus에서 AMQP 1.0이 지원되므로 효율적인 이진 프로토콜을 사용하여 다양한 플랫폼에서 큐 및 게시/구독 조정된 메시징 기능을 이용할 수 있습니다. 뿐만 아니라 여러 언어, 프레임워크 및 운영 체제가 혼합되어 사용된 구성 요소로 이루어진 애플리케이션을 만들 수 있습니다.

이 문서에서는 널리 사용되는 JMS(Java Message Service) API 표준을 통해 Java 애플리케이션에서 Service Bus 메시징 기능(큐 및 게시/구독 토픽)을 사용하는 방법을 설명합니다. Service Bus .NET API를 사용하여 동일한 작업을 수행하는 방법을 설명하는 [동반 문서](service-bus-amqp-dotnet.md)가 있습니다. AMQP 1.0을 사용한 플랫폼 간 메시징에 대해 알아보려면 이 두 가지 가이드를 함께 사용할 수 있습니다.

## <a name="get-started-with-service-bus"></a>Service Bus 시작
이 가이드에서는 사용자가 **basicqueue**라는 큐가 포함된 Service Bus 네임스페이스를 이미 가지고 있다고 가정합니다. 가지고 있지 않은 사용자는 [Azure Portal](https://portal.azure.com)을 사용하여 [네임스페이스와 큐를 만들](service-bus-create-namespace-portal.md) 수 있습니다. Service Bus 네임스페이스와 큐를 만드는 방법에 대한 자세한 내용은 [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)을 참조하세요.

> [!NOTE]
> 분할된 큐 및 토픽은 또한 AMQP를 지원합니다. 자세한 내용은 [분할된 메시징 엔터티](service-bus-partitioning.md) 및 [Service Bus 분할 큐 및 토픽을 위한 AMQP 1.0 지원](service-bus-partitioned-queues-and-topics-amqp-overview.md)을 참조하세요.
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>AMQP 1.0 JMS 클라이언트 라이브러리 다운로드
최신 버전의 Apache Qpid JMS AMQP 1.0 클라이언트 라이브러리를 다운로드할 위치에 대한 자세한 내용은 [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)을 참조하세요.

Service Bus를 사용하여 JMS 애플리케이션을 빌드 및 실행할 때 Apache Qpid JMS AMQP 1.0 배포 보관에 포함된 다음 JAR 파일 4개를 Java CLASSPATH에 추가해야 합니다.

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> ![참고] JMS JAR 이름 및 버전이 변경되었을 수 있습니다. 자세한 내용은 [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)을 참조하세요.

## <a name="coding-java-applications"></a>Java 애플리케이션 코딩
### <a name="java-naming-and-directory-interface-jndi"></a>JNDI(Java Naming and Directory Interface)
JMS는 JNDI(Java Naming and Directory Interface)를 사용하여 논리적 이름과 물리적 이름 간에 구분을 만듭니다. JNDI를 사용하여 두 유형의 JMS 개체인 ConnectionFactory와 Destination을 확인합니다. JNDI는 다양한 디렉터리 서비스를 연결할 수 있는 공급자 모델을 사용하여 이름 확인 책임을 처리합니다. Apache Qpid JMS AMQP 1.0 라이브러리에는 다음 형식의 속성 파일을 사용하여 구성된 간단한 속성 파일 기반 JNDI 공급자가 포함되어 있습니다.

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>JNDI 컨텍스트 설정 및 ConnectionFactory 구성

**기본 연결 문자열** 아래 [Azure Portal](https://portal.azure.com)의 ‘공유 액세스 정책’에서 제공되는 정책에 참조되는 **연결 문자열**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
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
Qpid 속성 파일 JNDI 공급자에서 destination을 정의하는 데 사용되는 항목의 형식은 다음과 같습니다.

생산자의 대상 큐를 만들려면 - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

소비자의 대상 큐를 만들려면 - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>JMS 애플리케이션 작성
Service Bus와 함께 JMS를 사용할 때 필요한 특별한 API 또는 옵션은 없습니다. 하지만 나중에 다룰 몇 가지 제한은 있습니다. JMS 애플리케이션과 마찬가지로 **ConnectionFactory** 및 대상을 확인하려면 먼저 JNDI 환경의 구성이 필요합니다.

#### <a name="configure-the-jndi-initialcontext"></a>JNDI InitialContext 구성
JNDI 환경은 구성 정보 해시 테이블을 javax.naming.InitialContext 클래스의 생성자에 전달하여 구성됩니다. 해시 테이블의 두 가지 필수 요소는 초기 컨텍스트 팩터리의 클래스 이름과 공급자 URL입니다. 다음 코드는 **servicebus.properties**라는 속성 파일과 함께 Qpid 속성 파일 기반 JNDI 공급자를 사용하도록 JNDI 환경을 구성하는 방법을 보여 줍니다.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Service Bus 큐를 사용하는 간단한 JMS 애플리케이션
다음 예제 프로그램은 JNDI 논리적 이름이 QUEUE인 Service Bus 큐에 JMS TextMessages를 보내고 메시지를 받습니다.

[Azure Service Bus 샘플 JMS 큐 빠른 시작](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)에서 모든 소스 코드 및 구성 정보에 액세스할 수 있습니다.

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
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
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
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
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
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
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

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
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
애플리케이션을 실행하면 다음과 같은 양식 출력이 표시됩니다.

```
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
다음은 AMQP 처리가 Service Bus 작업으로 변환되는 방법입니다.

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```


## <a name="unsupported-features-and-restrictions"></a>지원되지 않는 기능 및 제한
Service Bus와 함께 JMS over AMQP 1.0을 사용하는 경우 다음과 같은 제한 사항이 있습니다.

* **세션**당 하나의 **MessageProducer** 또는 **MessageConsumer**만 허용됩니다. 애플리케이션에서 **MessageProducers** 또는 **MessageConsumers**를 여러 개 만들어야 하는 경우 각 항목에 대한 전용 **세션**을 만듭니다.
* 휘발성 토픽 구독은 현재 지원되지 않습니다.
* **MessageSelectors**는 현재 지원되지 않습니다.
* 트랜잭션 처리된 세션과 분산 트랜잭션은 지원되지 않습니다.

또한 Azure Service Bus는 데이터 평면에서 제어 평면을 분리하므로 여러 JMS의 동적 토폴로지 함수를 지원하지 않습니다.

| 지원되지 않는 메서드          | 다음 항목으로 교체                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | 메시지 선택기를 포팅하는 토픽 구독을 만듭니다.                                 |
| createDurableConsumer       | 메시지 선택기를 포팅하는 토픽 구독을 만듭니다.                                 |
| createSharedConsumer        | Service Bus 토픽은 항상 공유 가능합니다(위의 항목 참조).                                       |
| createSharedDurableConsumer | Service Bus 토픽은 항상 공유 가능합니다(위의 항목 참조).                                       |
| createTemporaryTopic        | *AutoDeleteOnIdle*이 만료 기간으로 설정된 관리 API/도구/포털을 통해 토픽을 만듭니다. |
| createTopic                 | 관리 API/도구/포털을 통해 토픽을 만듭니다.                                           |
| unsubscribe                 | 토픽 관리 API/도구/포털을 삭제합니다.                                             |
| createBrowser               | 지원되지 않습니다. Service Bus API의 Peek() 기능을 사용합니다.                         |
| createQueue                 | 관리 API/도구/포털을 통해 큐를 만듭니다.                                           | 
| createTemporaryQueue        | *AutoDeleteOnIdle*이 만료 기간으로 설정된 관리 API/도구/포털을 통해 큐를 만듭니다. |

## <a name="summary"></a>요약
이 방법 가이드에서는 널리 사용되는 JMS API 및 AMQP 1.0을 통해 Java에서 Service Bus 조정된 메시징 기능(큐 및 게시/구독 토픽)에 액세스하는 방법을 설명했습니다.

.NET, C, Python, PHP 등의 다른 언어에서도 Service Bus AMQP 1.0을 사용할 수 있습니다. 이러한 언어로 빌드한 구성 요소는 Service Bus의 AMQP 1.0 지원을 사용하여 안정적이며 완전히 신뢰할 수 있는 상태로 메시지를 교환할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Service Bus의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [Service Bus .NET API와 함께 AMQP 1.0을 사용하는 방법](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 개발자 가이드](service-bus-amqp-dotnet.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Java 개발자 센터](https://azure.microsoft.com/develop/java/)

