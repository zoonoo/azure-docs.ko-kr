---
title: 아파치 카프카에 대한 Azure 이벤트 허브문제 해결
description: 이 문서에서는 아파치 카프카에 대 한 Azure 이벤트 허브와 문제를 해결 하는 방법을 보여 주어
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
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606731"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>이벤트 허브에 대한 아파치 카프카 문제 해결 가이드
이 문서에서는 아파치 카프카에 이벤트 허브를 사용할 때 발생할 수 있는 문제에 대한 문제 해결 팁을 제공합니다. 

## <a name="server-busy-exception"></a>서버 사용 중 이예외
Kafka 제한으로 인해 서버 사용 중 예외가 발생할 수 있습니다. AMQP 클라이언트를 사용하면 이벤트 허브는 서비스 제한 시 **서버 사용 중** 예외를 즉시 반환합니다. "나중에 다시 시도" 메시지와 동일합니다. 카프카에서는 메시지가 완료되기 전에 지연됩니다. 지연 길이는 생성/가져오기 `throttle_time_ms` 응답에서와 같이 밀리초 단위로 반환됩니다. 대부분의 경우 이러한 지연된 요청은 Event Hubs 대시보드에서 ServerBusy 예외로 기록되지 않습니다. 대신 응답값을 `throttle_time_ms` 처리량이 프로비저닝된 할당량을 초과했다는 표시기로 사용해야 합니다.

트래픽이 과도하면 서비스에 다음과 같은 동작이 있습니다.

- 생성 요청의 지연이 요청 시간 초과시 이벤트 허브는 **정책 위반** 오류 코드를 반환합니다.
- 가져오기 요청의 지연이 요청 시간 제한을 초과하는 경우 Event Hubs는 요청을 제한된 것으로 기록하고 빈 레코드 집합과 오류 코드 없이 응답합니다.

[전용 클러스터에는](event-hubs-dedicated-overview.md) 제한 메커니즘이 없습니다. 모든 클러스터 리소스를 자유롭게 사용할 수 있습니다.

## <a name="no-records-received"></a>수신된 레코드 없음
소비자가 레코드를 얻지 못하고 지속적으로 재조정되는 것을 볼 수 있습니다. 이 시나리오에서 소비자는 레코드를 얻지 못하고 지속적으로 재조정합니다. 이 경우 예외 또는 오류가 없지만 Kafka 로그는 소비자가 그룹에 다시 가입하고 파티션을 할당하려고 애쓰고 있음을 보여줍니다. 몇 가지 가능한 원인이 있습니다.

- 최소 권장 `request.timeout.ms` 값인 60000이고 최소 권장 `session.timeout.ms` 값인 30000값인지 확인합니다. 이러한 설정을 너무 낮게 설정하면 소비자 시간 시간이 발생하여 재조정이 발생할 수 있습니다(이로 인해 시간 시간이 더 많이 발생하여 재조정이 더 많이 발생합니다.) 
- 구성이 권장 값과 일치하고 지속적인 재조정이 계속 되면 문제를 열어보십시오(디버깅을 도울 수 있도록 문제에 전체 구성을 포함해야 합니다).

## <a name="compressionmessage-format-version-issue"></a>압축/메시지 형식 버전 문제
카프카는 압축을 지원하며, 카프카의 이벤트 허브는 현재 지원되지 않습니다. 메시지 형식 버전(예: `The message format version on the broker does not support the request.`클라이언트가 압축된 Kafka 메시지를 브로커에게 보내려고 할 때)을 언급하는 오류는 발생합니다.

압축된 데이터가 필요한 경우 브로커에게 데이터를 보내기 전에 데이터를 압축하고 수신 후 압축을 풀면 유효한 해결 방법입니다. 메시지 본문은 서비스에 대한 바이트 배열일 뿐이므로 클라이언트 측 압축/압축 해제는 문제를 일으키지 않습니다.

## <a name="unknownserverexception"></a>알 수 없는 서버예외
다음 예제와 유사한 Kafka 클라이언트 라이브러리에서 UnknownServerException을 받을 수 있습니다. 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Microsoft 지원으로 티켓을 엽니다.  UTC의 디버그 수준 로깅 및 예외 타임스탬프는 문제를 디버깅하는 데 유용합니다. 

## <a name="other-issues"></a>기타 문제
이벤트 허브에서 Kafka를 사용할 때 문제가 표시되면 다음 항목을 확인하십시오.

- **방화벽 트래픽 차단** - 포트 **9093이** 방화벽에 의해 차단되지 않았는지 확인합니다.
- **Topic권한예외** - 이 예외의 가장 일반적인 원인은 다음과 같습니다.
    - 구성 파일의 연결 문자열에 있는 오타 또는
    - 기본 계층 네임스페이스에서 Kafka에 이벤트 허브를 사용하려고 합니다. Kafka의 이벤트 허브는 [표준 및 전용 계층 네임스페이스에서만 지원됩니다.](https://azure.microsoft.com/pricing/details/event-hubs/)
- **카프카 버전 불일치** - 카프카 생태계에 대한 이벤트 허브는 카프카 버전 1.0 이상 을 지원합니다. Kafka 버전 0.10 이상을 사용하는 일부 응용 프로그램은 Kafka 프로토콜의 이전 버전 호환성으로 인해 때때로 작동할 수 있지만 이전 API 버전을 사용하지 않는 것이 좋습니다. Kafka 버전 0.9 및 이전 버전에는 필요한 SASL 프로토콜을 지원하지 않으며 이벤트 허브에 연결할 수 없습니다.
- **카프카와 함께 사용하는 AMQP 헤더에 대한 이상한 인코딩** - AMQP를 통해 이벤트 허브로 이벤트를 보낼 때 AMQP 페이로드 헤더는 AMQP 인코딩에서 직렬화됩니다. 카프카 소비자는 AMQP에서 헤더를 직렬화하지 않습니다. 헤더 값을 읽으려면 AMQP 헤더를 수동으로 디코딩합니다. 또는 Kafka 프로토콜을 통해 사용할 수 있다는 것을 알고 있는 경우 AMQP 헤더를 사용하지 않도록 할 수 있습니다. 자세한 내용은 [이 GitHub 문제를](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56)참조하십시오.
- **SASL 인증** - 이벤트 허브에서 요구하는 SASL 인증 프로토콜과 협력하기 위해 프레임워크를 얻는 것이 눈에 보이는 것보다 더 어려울 수 있습니다. SASL 인증에서 프레임워크의 리소스를 사용하여 구성 문제를 해결할 수 있는지 확인합니다. 

## <a name="limits"></a>제한
아파치 카프카 vs. 이벤트 허브 카프카. 대부분의 경우 카프카 생태계의 이벤트 허브는 아파치 카프카가 수행하는 기본값, 속성, 오류 코드 및 일반적인 동작과 동일합니다. 이 두 가지가 명시적으로 다른 경우(또는 이벤트 허브가 Kafka가 하지 않는 제한을 부과하는 경우)는 다음과 같습니다.

- `group.id` 속성의 최대 길이는 256자입니다.
- 최대 `offset.metadata.max.bytes` 크기는 1024바이트입니다.
- 오프셋 커밋은 최대 내부 로그 크기가 1MB인 파티션당 4호출/초로 제한됩니다.


## <a name="next-steps"></a>다음 단계
Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [이벤트 허브에 대한 아파치 카프카 개발자 가이드](apache-kafka-developer-guide.md)
- [이벤트 허브에 대한 아파치 카프카 마이그레이션 가이드](apache-kafka-migration-guide.md)
- [자주 묻는 질문 - 아파치 카프카의 이벤트 허브](apache-kafka-frequently-asked-questions.md)
- [권장 구성](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
