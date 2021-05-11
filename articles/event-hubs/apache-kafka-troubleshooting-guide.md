---
title: Apache Kafka용 Azure Event Hubs 관련 이슈 해결
description: 이 문서에서는 Apache Kafka용 Azure Event Hubs 관련 이슈를 해결하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a9d4a93f0074f206cd4627913505c66eb6480cbd
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314085"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Event Hubs에 대한 Apache Kafka 문제 해결 가이드
이 문서에서는 Apache Kafka용 Event Hubs를 사용할 때 발생할 수 있는 이슈에 대한 문제 해결 팁을 제공합니다. 

## <a name="server-busy-exception"></a>서버 작업 중 예외
Kafka 제한으로 인해 서버 작업 중 예외가 발생할 수 있습니다. AMQP 클라이언트를 사용하면 서비스 제한 시 Event Hubs가 즉시 **서버 작업 중** 예외를 반환합니다. “나중에 다시 시도” 메시지와 같습니다. Kafka에서는 메시지가 완료되기 전에 지연됩니다. 지연 길이는 생성/가져오기 응답에서 `throttle_time_ms`(밀리초)로 반환됩니다. 대부분의 경우 지연된 요청은 Event Hubs 대시보드에 서버 작업 중 예외로 로깅되지 않습니다. 대신, 응답의 `throttle_time_ms` 값을 처리량이 프로비저닝된 할당량을 초과했음을 나타내는 지표로 사용해야 합니다.

트래픽이 과도하면 서비스에서 다음 동작을 수행합니다.

- 생성 요청의 지연이 요청 시간 제한을 초과하면 Event Hubs에서 **정책 위반** 오류 코드를 반환합니다.
- 가져오기 요청의 지연이 요청 시간 제한을 초과하면 Event Hubs에서 요청을 제한된 것으로 로깅하고 오류 코드 없이 빈 레코드 집합으로 응답합니다.

[전용 클러스터](event-hubs-dedicated-overview.md)에는 제한 메커니즘이 없습니다. 모든 클러스터 리소스를 사용할 수 있습니다.

## <a name="no-records-received"></a>레코드가 수신되지 않음
소비자가 레코드를 가져와 계속해서 리밸런싱하지 않는 것을 확인할 수 있습니다. 이 시나리오에서는 소비자가 레코드를 가져와 계속해서 리밸런싱하지 않습니다. 이 경우 예외나 오류가 발생하지는 않지만 Kafka 로그에 소비자가 그룹에 다시 참가하여 파티션을 할당하려고 시도하다가 중단되었다고 표시됩니다. 몇 가지 가능한 원인은 다음과 같습니다.

- `request.timeout.ms`가 권장 값인 60000 이상이고 `session.timeout.ms`가 권장 값인 30000 이상인지 확인합니다. 두 설정이 너무 낮으면 소비자 시간 초과로 인해 리밸런싱이 발생할 수 있습니다. 이로 인해 더 많은 시간 초과와 리밸런싱이 발생하게 됩니다. 
- 구성이 권장 값과 일치하는데 계속해서 리밸런싱이 표시되는 경우 언제든지 이슈를 열 수 있습니다. 디버그에 도움이 되도록 이슈에 전체 구성을 포함해야 합니다.

## <a name="compressionmessage-format-version-issue"></a>압축/메시지 형식 버전 이슈
Kafka는 압축을 지원하고 Kafka용 Event Hubs는 현재 압축을 지원하지 않습니다. 메시지 형식 버전을 언급하는 오류(예: `The message format version on the broker does not support the request.`)는 클라이언트가 압축된 Kafka 메시지를 broker에게 보내려고 할 때 발생합니다.

압축된 데이터가 필요한 경우 broker에게 보내기 전에 데이터를 압축했다가 받은 후에 압축을 풀면 유효한 해결 방법이 됩니다. 메시지 본문은 단순히 서비스에 대한 바이트 배열이므로 클라이언트 쪽 압축/압축 풀기로 인한 이슈는 발생하지 않습니다.

## <a name="unknownserverexception"></a>UnknownServerException
Kafka 클라이언트 라이브러리로부터 다음 예제와 유사한 UnknownServerException을 받을 수 있습니다. 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Microsoft 지원에서 티켓을 엽니다.  UTC의 디버그 수준 로깅과 예외 타임스탬프는 이슈를 디버그하는 데 도움이 됩니다. 

## <a name="other-issues"></a>기타 문제
Event Hubs에서 Kafka를 사용할 때 이슈가 발생하는 경우 다음 항목을 확인합니다.

- **트래픽 차단 방화벽** - 포트 **9093** 이 방화벽에서 차단되지 않았는지 확인합니다.
- **TopicAuthorizationException** - 이 예외의 가장 일반적인 원인은 다음과 같습니다.
    - 구성 파일의 연결 문자열에 오타가 있음 또는
    - 기본 계층 네임스페이스에서 Kafka용 Event Hubs를 사용하려고 함. Kafka용 Event Hubs 기능은 [표준 및 전용 계층 네임스페이스에서만 지원](https://azure.microsoft.com/pricing/details/event-hubs/)됩니다.
- **Kafka 버전 불일치** - Kafka용 Event Hubs 에코시스템은 Kafka 버전 1.0 이상을 지원합니다. Kafka 버전 0.10 이상을 사용하는 일부 애플리케이션도 Kafka 프로토콜이 지원하는 이전 버전과의 호환성으로 인해 가끔 작동할 수 있지만 이전 API 버전을 사용하지 않는 것이 좋습니다. Kafka 버전 0.9 및 이전 버전은 필요한 SASL 프로토콜을 지원하지 않으며 Event Hubs에 연결할 수 없습니다.
- **Kafka에서 사용하는 경우 AMQP 헤더의 이상한 인코딩** - AMQP를 통해 이벤트 허브에 이벤트를 보내는 경우 모든 AMQP 페이로드 헤더는 AMQP 인코딩으로 직렬화됩니다. Kafka 소비자는 AMQP 헤더를 역직렬화하지 않습니다. 헤더 값을 읽으려면 AMQP 헤더를 수동으로 디코드합니다. 또는 Kafka 프로토콜을 통해 사용할 것을 알고 있는 경우 AMQP 헤더를 사용하지 않을 수 있습니다. 자세한 내용은 [이 GitHub 이슈](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)를 참조하세요.
- **SASL 인증** - Event Hubs에서 요구하는 SASL 인증 프로토콜과 프레임워크를 연동시키는 것은 보이는 것보다 더 어려울 수 있습니다. SASL 인증에 대한 프레임워크 리소스를 사용하여 구성 문제를 해결할 수 있는지 확인합니다. 

## <a name="limits"></a>제한
Apache Kafka 대 Event Hubs Kafka. 대부분의 경우 Azure Event Hubs의 Kafka 인터페이스는 Apache Kafka와 동일한 기본값, 속성, 오류 코드, 일반 동작을 사용합니다. 아래에는 두 항목 간 차이가 명시적으로 나타나거나 Kafka에서 적용되지 않는 제한이 Event Hubs에서 적용되는 인스턴스가 나와 있습니다.

- `group.id` 속성의 최대 길이는 256자입니다.
- `offset.metadata.max.bytes`의 최대 크기는 1024바이트입니다.
- 오프셋 커밋은 파티션당 4회 호출/초로 제한되고 최대 내부 로그 크기는 1MB입니다.


## <a name="next-steps"></a>다음 단계
Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [Event Hubs에 대한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)
- [Event Hubs에 대한 Apache Kafka 마이그레이션 가이드](apache-kafka-migration-guide.md)
- [질문과 대답 - Apache Kafka용 Event Hubs](apache-kafka-frequently-asked-questions.yml)
- [권장 구성](apache-kafka-configurations.md)
