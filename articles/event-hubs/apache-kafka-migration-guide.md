---
title: Apache Kafka에 대 한 Azure Event Hubs로 마이그레이션
description: 이 문서에서는 Apache Kafka에서 Azure Event Hubs로 클라이언트를 마이그레이션하는 방법을 설명 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d9f3775a85df5a881c2c38566628e4e1d4d8c40e
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061447"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Apache Kafka 에코시스템용 Azure Event Hubs로 마이그레이션
Azure Event Hubs는 Kafka 프로토콜을 사용 하 여 Event Hubs에 연결 하는 데 사용할 수 있는 Apache Kafka 엔드포인트를 노출 합니다. 기존 Kafka 응용 프로그램을 최소한으로 변경 하 여 Azure Event Hubs에 연결 하 고 Azure 에코 시스템의 이점을 누릴 수 있습니다. Event Hubs MirrorMaker를 포함 하 여 기존의 여러 Kafka 응용 프로그램에서 작동 합니다. 자세한 내용은 [Event Hubs Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) 를 참조 하세요.

## <a name="pre-migration"></a>사전 마이그레이션 

### <a name="create-an-azure-account"></a>Azure 계정 만들기
Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

### <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기
[이벤트 허브 만들기](event-hubs-create.md) 문서의 단계별 지침에 따라 Event Hubs 네임 스페이스 및 이벤트 허브를 만듭니다. 

### <a name="connection-string"></a>연결 문자열
[포털에서 연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 문서의 단계를 따릅니다. 나중에 사용 하기 위해 연결 문자열을 적어 둡니다. 

### <a name="fully-qualified-domain-name-fqdn"></a>FQDN (정규화 된 도메인 이름)
Event Hub 네임 스페이스를 가리키는 FQDN도 필요할 수 있습니다. FQDN은 다음과 같이 연결 문자열 내에서 찾을 수 있습니다.

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Event Hubs 네임 스페이스가 공용이 아닌 클라우드에서 배포 되는 경우 도메인 이름은 다를 수 있습니다 (예: \* . servicebus.chinacloudapi.cn, \* servicebus.usgovcloudapi.net 또는 \* servicebus.cloudapi.de).

## <a name="migration"></a>마이그레이션 

### <a name="update-your-kafka-client-configuration"></a>Kafka 클라이언트 구성 업데이트

Kafka 사용 이벤트 허브에 연결 하려면 Kafka 클라이언트 구성을 업데이트 해야 합니다. 사용자를 찾는 데 문제가 있는 경우 `bootstrap.servers` 응용 프로그램에서가 설정 된 위치를 검색 해 보세요.

응용 프로그램에서 적합 한 위치에 다음 configs을 삽입 합니다. `bootstrap.servers` `sasl.jaas.config` 클라이언트를 올바른 인증을 사용 하 여 Event Hubs Kafka 끝점으로 보내도록 및 값을 업데이트 해야 합니다. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

`sasl.jaas.config`에서 프레임 워크의 구성이 지원 되지 않는 경우 SASL 사용자 이름 및 암호를 설정 하는 데 사용 되는 구성을 찾아 대신 사용 합니다. 사용자 이름을로 설정 하 `$ConnectionString` 고 암호를 Event Hubs 연결 문자열로 설정 합니다.

## <a name="post-migration"></a>마이그레이션 후 작업
이벤트 허브로 이벤트를 전송 하는 Kafka 응용 프로그램을 실행 합니다. 그런 다음 이벤트 허브가 Azure Portal를 사용 하 여 이벤트를 수신 하는지 확인 합니다. Event Hubs 네임 스페이스의 **개요** 페이지에서 **메트릭** 섹션의 **메시지** 뷰로 전환 합니다. 페이지를 새로 고쳐 차트를 업데이트합니다. 메시지가 수신되었다는 내용이 표시될 때까지 몇 초 정도 걸릴 수 있습니다. 

[![이벤트 허브에서 메시지를 수신했는지 확인](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>다음 단계
Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [Event Hubs에 대 한 Apache Kafka 문제 해결 가이드](apache-kafka-troubleshooting-guide.md)
- [Faq (질문과 대답) Event Hubs Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Azure Event Hubs에 대 한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)
- [권장 구성](apache-kafka-configurations.md)