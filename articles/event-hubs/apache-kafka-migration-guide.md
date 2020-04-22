---
title: 아파치 카프카에 대 한 Azure 이벤트 허브로 마이그레이션
description: 이 문서에서는 서로 다른 프로토콜(AMQP, Apache Kafka 및 HTTPS)을 사용하는 소비자와 생산자가 Azure Event Hubs를 사용할 때 이벤트를 교환하는 방법을 보여 줍니다.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 32b08e565b86af8f6373c9848211646128bb346d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677364"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>아파치 카프카 생태계를 위한 Azure 이벤트 허브로 마이그레이션
Azure 이벤트 허브는 카프카 프로토콜을 사용하여 이벤트 허브에 연결할 수 있는 아파치 카프카 엔드포인트를 노출합니다. 기존 Kafka 응용 프로그램을 최소한으로 변경하면 Azure 이벤트 허브에 연결하고 Azure 에코시스템의 이점을 얻을 수 있습니다. 카프카에 대한 이벤트 허브는 [아파치 카프카 버전 1.0](https://kafka.apache.org/10/documentation.html) 이상 을 지원합니다.

## <a name="pre-migration"></a>사전 마이그레이션 

### <a name="create-an-azure-account"></a>Azure 계정 만들기
Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

### <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기
[이벤트 허브 만들기](event-hubs-create.md) 문서에서 단계별 지침을 따라 이벤트 허브 네임스페이스 및 이벤트 허브를 만듭니다. 

### <a name="connection-string"></a>연결 문자열
포털 문서에서 [연결 받기 문자열의 단계를 따릅니다.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 또한 나중에 사용할 수 있는 연결 문자열을 적어 둡자하십시오. 

### <a name="fully-qualified-domain-name-fqdn"></a>정규화된 도메인 이름(FQDN)
이벤트 허브 네임스페이스를 가리키는 FQDN이 필요할 수도 있습니다. FQDN은 다음과 같이 연결 문자열 내에서 찾을 수 있습니다.

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

이벤트 허브 네임스페이스가 비공개 클라우드에 배포된 경우 도메인 이름이 다를 \*수 있습니다(예: \* \*.servicebus.chinacloudapi.cn, .servicebus.usgovcloudapi.net 또는 .servicebus.cloudapi.de).

## <a name="migration"></a>마이그레이션 

### <a name="update-your-kafka-client-configuration"></a>Kafka 클라이언트 구성 업데이트

Kafka 지원 이벤트 허브에 연결하려면 Kafka 클라이언트 구성을 업데이트해야 합니다. 검색에 문제가 있는 경우 응용 프로그램에서 설정된 `bootstrap.servers` 위치를 검색해 보십시오.

응용 프로그램에서 의미가 있는 곳에 다음 구성을 삽입합니다. 올바른 인증을 통해 `bootstrap.servers` `sasl.jaas.config` 클라이언트를 이벤트 허브 Kafka 끝점으로 연결하도록 및 값을 업데이트해야 합니다. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

프레임워크에서 지원되는 구성이 아닌 경우 `sasl.jaas.config` SASL 사용자 이름과 암호를 설정하는 데 사용되는 구성을 찾아 대신 사용합니다. 사용자 이름과 `$ConnectionString` 암호를 이벤트 허브 연결 문자열로 설정합니다.

## <a name="post-migration"></a>마이그레이션 후 작업
이벤트 허브로 이벤트를 보내는 Kafka 응용 프로그램을 실행합니다. 그런 다음 이벤트 허브가 Azure 포털을 사용하여 이벤트를 수신하는지 확인합니다. 이벤트 허브 네임스페이스의 **개요** 페이지에서 메트릭 섹션의 **메시지** 보기로 **전환합니다.** 페이지를 새로 고쳐 차트를 업데이트합니다. 메시지가 수신되었다는 내용이 표시될 때까지 몇 초 정도 걸릴 수 있습니다. 

[![이벤트 허브에서 메시지를 수신했는지 확인](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>다음 단계
Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [이벤트 허브에 대한 아파치 카프카 문제 해결 가이드](apache-kafka-troubleshooting-guide.md)
- [자주 묻는 질문 - 아파치 카프카의 이벤트 허브](apache-kafka-frequently-asked-questions.md)
- [Azure 이벤트 허브에 대 한 아파치 카프카 개발자 가이드](apache-kafka-developer-guide.md)
- [권장 구성](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)