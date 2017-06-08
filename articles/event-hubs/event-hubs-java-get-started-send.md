---
title: "Java를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs"
description: "Java를 사용하여 Event Hubs로 전송 시작"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 45776b0920f65ae9749b00978656bcefa2bf01a8
ms.contentlocale: ko-kr
ms.lasthandoff: 05/04/2017


---

# <a name="send-events-to-azure-event-hubs-using-java"></a>Java를 사용하여 Azure Event Hubs로 이벤트 전송

## <a name="introduction"></a>소개
이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [이벤트 허브 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 Java 언어의 콘솔 응용 프로그램을 사용하여 이벤트 허브로 이벤트를 전송하는 방법을 보여 줍니다. Java 이벤트 프로세스 호스트 라이브러리를 사용하여 이벤트를 수신하려면 [이 문서](event-hubs-java-get-started-receive-eph.md)를 참조하거나 목차 왼쪽에서 해당하는 수신 언어를 클릭합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용한다고 가정합니다.
* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 체험</a>을 참조하세요.

## <a name="send-messages-to-event-hubs"></a>이벤트 허브에 메시지 보내기
이벤트 허브에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)의 Maven 프로젝트에 사용할 수 있으며 Maven 프로젝트 파일 내의 다음 종속성 선언을 사용하여 참조할 수 있습니다.    

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

다양한 유형의 빌드 환경을 위해, [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) 또는 [GitHub의 릴리스 배포 지점](https://github.com/Azure/azure-event-hubs/releases)에서 최근에 릴리스된 JAR 파일을 명시적으로 가져올 수 있습니다.  

단순 이벤트 게시자의 경우 이벤트 허브 클라이언트 클래스에 대한 *com.microsoft.azure.eventhubs* 패키지와 유틸리티 클래스(예: Azure Service Bus 메시징 클라이언트와 공유되는 일반적인 예외)에 대한 *com.microsoft.azure.servicebus* 패키지를 가져옵니다. 

다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 응용 프로그램에 대한 새 Maven 프로젝트를 만듭니다. 이 클래스를 `Send`라고 합니다.     

```Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

네임스페이스 및 이벤트 허브 이름을 이벤트 허브를 만들 때 사용한 값으로 바꿉니다.

```Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

그런 후 문자열을 UTF-8 바이트 인코딩으로 전환하여 단일 이벤트를 만듭니다. 그런 다음 연결 문자열에서 새 이벤트 허브 클라이언트 인스턴스를 만들고 해당 메시지를 보냅니다.   

```Java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [를 사용하여 이벤트 수신](event-hubs-java-get-started-receive-eph.md)
* [Event Hubs 개요][Event Hubs overview]
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
