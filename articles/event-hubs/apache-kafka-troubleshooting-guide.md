---
title: Apache Kafka에 대 한 Azure Event Hubs 문제 해결
description: 이 문서에서는 Apache Kafka에 대해 Azure Event Hubs 문제를 해결 하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: c2403fd51729ef8809b9a70383ad6f9fd91e52b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322679"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Event Hubs에 대 한 Apache Kafka 문제 해결 가이드
이 문서에서는 Apache Kafka에 대해 Event Hubs를 사용할 때 발생할 수 있는 문제에 대 한 문제 해결 팁을 제공 합니다. 

## <a name="server-busy-exception"></a>서버 작업 중 예외
Kafka 제한으로 인해 서버 사용 중 예외가 발생할 수 있습니다. AMQP 클라이언트를 사용 하면 서비스를 제한할 때 **서버 사용 중** 예외가 즉시 반환 Event Hubs. "나중에 다시 시도" 메시지와 동일 합니다. Kafka에서 메시지는 완료 되기 전에 지연 됩니다. 지연 길이는 `throttle_time_ms` 생성/인출 응답에서 밀리초로 반환 됩니다. 대부분의 경우 이러한 지연 된 요청은 Event Hubs 대시보드의 ServerBusy 예외로 기록 되지 않습니다. 대신 응답의 값을 `throttle_time_ms` 처리량이 프로 비전 된 할당량을 초과 했음을 나타내는 표시기로 사용 해야 합니다.

트래픽이 과도 한 경우 서비스는 다음과 같은 동작을 수행 합니다.

- 요청 지연 시간이 요청 시간 제한을 초과 하는 경우 Event Hubs **정책 위반** 오류 코드를 반환 합니다.
- Fetch 요청의 지연이 요청 시간 제한을 초과 하는 경우 Event Hubs은 요청을 제한 된 것으로 기록 하 고 빈 레코드 집합을 사용 하 여 응답 하며 오류 코드는 기록 하지 않습니다.

[전용 클러스터](event-hubs-dedicated-overview.md) 에는 조정 메커니즘이 없습니다. 모든 클러스터 리소스를 사용할 수 있습니다.

## <a name="no-records-received"></a>받은 레코드 없음
소비자가 레코드를 받고 지속적으로 균형을 재조정 하지 않는 것을 볼 수 있습니다. 이 시나리오에서 소비자는 레코드를 받고 지속적으로 균형을 다시 조정 하지 않습니다. 예외 나 오류는 발생 하지 않지만, Kafka logs는 소비자가 그룹에 다시 참가 하 고 파티션을 할당 하려고 하는 것을 중지 하 고 있음을 보여 줍니다. 몇 가지 가능한 원인은 다음과 같습니다.

- 이 `request.timeout.ms` 6만의 권장 값 이상 인지 확인 하 고, `session.timeout.ms` 최소 권장 값은 3만입니다. 이러한 설정을 너무 낮게 설정 하면 소비자 시간 초과가 발생 하 여 다시 잔액을 초래 하 게 됩니다 .이로 인해 시간 초과가 발생 하 여 더 많은 균형을 유지 하 게 됩니다. 
- 구성이 권장 되는 값과 일치 하는 경우 계속 해 서 지속적으로 균형을 조정 하는 것을 볼 수 있습니다. 문제를 쉽게 해결할 수 있도록 문제에 전체 구성을 포함 해야 합니다.

## <a name="compressionmessage-format-version-issue"></a>압축/메시지 형식 버전 문제
Kafka는 압축을 지원 하 고 현재 Kafka에 대 한 Event Hubs는 그렇지 않습니다. 메시지 형식 버전을 언급 하는 오류 (예: `The message format version on the broker does not support the request.` )는 클라이언트가 압축 된 Kafka 메시지를 broker로 보내려고 할 때 발생 합니다.

압축 된 데이터가 필요한 경우 broker에 전송 하기 전에 데이터를 압축 하 고 수신 후에 압축을 푸는 것이 올바른 해결 방법입니다. 메시지 본문은 서비스에 대 한 바이트 배열인 것 이므로 클라이언트 쪽 압축/압축 풀기로 인해 문제가 발생 하지 않습니다.

## <a name="unknownserverexception"></a>UnknownServerException
다음 예제와 비슷한 Kafka 클라이언트 라이브러리에서 UnknownServerException을 받을 수 있습니다. 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Microsoft 지원으로 티켓을 엽니다.  UTC의 디버그 수준 로깅 및 예외 타임 스탬프는 문제를 디버깅 하는 데 유용 합니다. 

## <a name="other-issues"></a>기타 문제
Event Hubs에서 Kafka을 사용할 때 문제가 발생 하는 경우 다음 항목을 확인 하십시오.

- **방화벽 차단 트래픽** -포트 **9093** 이 방화벽에 의해 차단 되지 않았는지 확인 합니다.
- **TopicAuthorizationException** -이 예외의 가장 일반적인 원인은 다음과 같습니다.
    - 구성 파일에 있는 연결 문자열의 오타가 나
    - 기본 계층 네임 스페이스의 Kafka에 Event Hubs를 사용 하려고 합니다. Kafka에 대 한 Event Hubs은 [표준 및 전용 계층 네임 스페이스에만 지원](https://azure.microsoft.com/pricing/details/event-hubs/)됩니다.
- **Kafka 버전 불일치** -Kafka에 대 한 Event Hubs kafka 버전 1.0 이상을 지원 합니다. Kafka 버전 0.10 이상을 사용 하는 일부 응용 프로그램은 kafverprotocol의 이전 버전과의 호환성으로 인해 가끔씩 작동할 수 있지만 이전 API 버전을 사용 하지 않는 것이 좋습니다. Kafka 버전 0.9 및 이전 버전은 필요한 SASL 프로토콜을 지원 하지 않으며 Event Hubs에 연결할 수 없습니다.
- **Kafka에서 사용 하는 경우 amqp 헤더에서 이상한 인코딩** -amqp를 통해 이벤트 허브로 이벤트를 보낼 때 amqp 페이로드 헤더는 amqp 인코딩으로 직렬화 됩니다. Kafka 소비자는 AMQP에서 헤더를 deserialize 하지 않습니다. 헤더 값을 읽으려면 AMQP 헤더를 수동으로 디코딩합니다. 또는 Kafka 프로토콜을 통해 사용할 것으로 알고 있는 경우 AMQP 헤더를 사용 하지 않을 수 있습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)를 참조하세요.
- **Sasl 인증** -Event Hubs에서 요구 하는 sasl 인증 프로토콜을 사용 하 여 프레임 워크를 가져오는 것이 눈동자를 충족 하는 것 보다 더 어려울 수 있습니다. SASL 인증에서 프레임 워크의 리소스를 사용 하 여 구성 문제를 해결할 수 있는지 확인 합니다. 

## <a name="limits"></a>제한
Apache Kafka와 Event Hubs Kafka을 비교 합니다. 대부분의 경우 Kafka 에코 시스템에 대 한 Event Hubs에는 Apache Kafka에서 수행 하는 것과 동일한 기본값, 속성, 오류 코드 및 일반 동작이 있습니다. 이러한 두 값이 명시적으로 다른 인스턴스 (또는 Kafka이 아닌 제한이 적용 되는 Event Hubs)는 아래에 나열 되어 있습니다.

- 속성의 최대 길이는 `group.id` 256 자입니다.
- 최대 크기는 `offset.metadata.max.bytes` 1024 바이트입니다.
- 오프셋 커밋은 최대 내부 로그 크기인 1mb를 사용 하는 파티션당 4 개의 calls/second로 제한 됩니다.


## <a name="next-steps"></a>다음 단계
Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [Event Hubs에 대 한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)
- [Event Hubs에 대 한 Apache Kafka 마이그레이션 가이드](apache-kafka-migration-guide.md)
- [Faq (질문과 대답) Event Hubs Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [권장 구성](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
