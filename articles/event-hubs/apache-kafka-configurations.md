---
title: Apache Kafka 클라이언트에 권장 되는 구성-Azure Event Hubs
description: 이 문서에서는 Apache Kafka에 대 한 Azure Event Hubs와 상호 작용 하는 클라이언트에 권장 되는 Apache Kafka 구성을 제공 합니다.
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: f9a03d1d3433461a575b32cd69893408a8b0ef97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097669"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Apache Kafka 클라이언트에 대 한 권장 구성
다음은 Apache Kafka 클라이언트 응용 프로그램에서 Azure Event Hubs를 사용 하기 위한 권장 구성입니다. 

## <a name="java-client-configuration-properties"></a>Java 클라이언트 구성 속성

### <a name="producer-and-consumer-configurations"></a>생산자 및 소비자 구성

속성 | 권장 값 | 허용 된 범위 | 메모
---|---:|-----:|---
`metadata.max.age.ms` | 18만 (근사치) | < 24만 | 더 낮출 수 있습니다.
`connections.max.idle.ms`   | 180000 | < 24만 | Azure는 인바운드 TCP 유휴 > 24만 밀리초를 닫음 .이로 인해 비활성 연결 (보내기 시간 제한으로 인해 만료 된 일괄 처리로 표시 됨)에서 전송 될 수 있습니다.

### <a name="producer-configurations-only"></a>생산자 구성만
생산자 configs는 [여기](https://kafka.apache.org/documentation/#producerconfigs)에서 찾을 수 있습니다.

속성 | 권장 값 | 허용 된 범위 | 메모
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | 1046528 바이트 보다 큰 요청이 전송 되 면 서비스에서 연결을 닫습니다.  *이 값은 변경 **해야** 하며 처리량이 큰 생성 시나리오에서 문제가 발생 합니다.*
`retries` | > 0 | | Delivery.timeout.ms 값을 늘려야 할 수 있습니다. 설명서를 참조 하세요.
`request.timeout.ms` | 3만. 60000 | > 2만| EH는 내부적으로 최소 2만 밀리초로 기본값을 만듭니다.  *제한 시간 값이 낮은 요청은 수락 되지만 클라이언트 동작은 보장 되지 않습니다.*
`metadata.max.idle.ms` | 180000 | > 5000 | 생산자가 유휴 상태인 토픽의 메타 데이터를 캐시 하는 기간을 제어 합니다. 항목이 마지막으로 생성 된 이후 경과 된 시간이 메타 데이터 유휴 기간을 초과 하는 경우 토픽의 메타 데이터는 무시 되 고 그 다음에 액세스 하면 메타 데이터 인출 요청이 강제로 수행 됩니다.
`linger.ms` | > 0 | | 처리량이 높은 시나리오에서는 일괄 처리를 활용 하기 위해 링거 값이 가장 높은 지속할 값과 같아야 합니다.
`delivery.timeout.ms` | | | 수식 () *에 따라 설정 `request.timeout.ms`  +  `linger.ms` `retries` 합니다.
`enable.idempotence` | false | | 멱 등 성는 현재 지원 되지 않습니다.
`compression.type` | `none` | | 현재 압축은 지원 되지 않습니다.

### <a name="consumer-configurations-only"></a>소비자 구성만
소비자 configs는 [여기](https://kafka.apache.org/documentation/#consumerconfigs)에서 찾을 수 있습니다.

속성 | 권장 값 | 허용 된 범위 | 메모
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000는 기본값이 며 변경할 수 없습니다.
`session.timeout.ms` | 30000 |6000. 300000| 3만부터 하트 비트 누락으로 인해 자주 균형 조정이 표시 되는 경우 증가 합니다.


## <a name="librdkafka-configuration-properties"></a>librdkafka 구성 속성
기본 `librdkafka` 구성 파일 ([링크](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md))에는 아래 속성에 대 한 확장 설명이 포함 되어 있습니다.

### <a name="producer-and-consumer-configurations"></a>생산자 및 소비자 구성

속성 | 권장 값 | 허용 된 범위 | 메모
---|---:|-----:|---
`socket.keepalive.enable` | true | | 연결이 유휴 상태가 될 것으로 예상 되는 경우에 필요 합니다.  Azure는 인바운드 TCP 유휴 > 24만 밀리초를 닫습니다.
`metadata.max.age.ms` | ~ 18만| < 24만 | 더 낮출 수 있습니다.

### <a name="producer-configurations-only"></a>생산자 구성만

속성 | 권장 값 | 허용 된 범위 | 메모
---|---:|-----:|---
`retries` | > 0 | | 기본값은 2입니다. 이 값을 유지 하는 것이 좋습니다. 
`request.timeout.ms` | 3만. 60000 | > 2만| EH는 내부적으로 최소 2만 밀리초로 기본값을 만듭니다.  `librdkafka` 기본값은 5000입니다 .이 값은 문제가 될 수 있습니다. *제한 시간 값이 낮은 요청은 수락 되지만 클라이언트 동작은 보장 되지 않습니다.*
`partitioner` | `consistent_random` | Librdkafka 설명서를 참조 하세요. | `consistent_random` 는 기본값이 며 가장 좋습니다.  대부분의 경우 비어 있는 키와 null 키를 처리 하는 것이 가장 좋습니다.
`enable.idempotence` | false | | 멱 등 성는 현재 지원 되지 않습니다.
`compression.codec` | `none` || 압축은 현재 지원 되지 않습니다.

### <a name="consumer-configurations-only"></a>소비자 구성만

속성 | 권장 값 | 허용 된 범위 | 메모
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000는 기본값이 며 변경할 수 없습니다.
`session.timeout.ms` | 30000 |6000. 300000| 3만부터 하트 비트 누락으로 인해 자주 균형 조정이 표시 되는 경우 증가 합니다.


## <a name="further-notes"></a>추가 참고 사항

일반적인 구성 관련 오류 시나리오는 다음 표를 참조 하십시오.

증상 | 문제 | 솔루션
----|---|-----
균형 재조정으로 인 한 오프셋 커밋 실패 | 소비자가 폴링 ()에 대 한 호출 사이에 너무 오래 대기 하 고 있으며, 서비스는 소비자를 그룹 외부로 시작 합니다. | 여러 옵션이 있습니다. <ul><li>세션 제한 시간 늘리기</li><li>처리 속도를 높이기 위해 메시지 일괄 처리 크기 줄이기</li><li>소비자 폴링합니다 () 차단을 방지 하기 위해 처리 병렬화 개선</li></ul> 이 세 가지 조합을 적용 하는 것이 wisest 가능성이 높습니다.
높은 처리량의 네트워크 예외 | Java 클라이언트 + 기본 max. 요청을 사용 하나요?  요청이 너무 클 수 있습니다. | 위의 Java configs를 참조 하세요.

## <a name="next-steps"></a>다음 단계
모든 Azure 서비스의 할당량 및 제한은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](..//azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요. 
