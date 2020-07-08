---
title: 질문과 대답-Azure Event Hubs Apache Kafka
description: 이 문서에서는 서로 다른 프로토콜(AMQP, Apache Kafka 및 HTTPS)을 사용하는 소비자와 생산자가 Azure Event Hubs를 사용할 때 이벤트를 교환하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 066024f657e011aac1449106ec32689f145c5534
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314184"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Faq (질문과 대답) Event Hubs Apache Kafka 
이 문서에서는 Apache Kafka에 대 한 Event Hubs로 마이그레이션하는 데 대 한 몇 가지 질문과 대답을 제공 합니다.

## <a name="do-you-run-apache-kafka"></a>Apache Kafka를 실행 하 시겠습니까?

아니요.  Event Hubs 인프라에 대해 Kafka API 작업을 실행 합니다.  Apache Kafka와 Event Hubs AMQP 기능 (즉, 생성, 수신, 관리 등) 간에 밀접 한 상관 관계가 있기 때문에, Event Hubs의 알려진 안정성을 Kafka PaaS 공간으로 가져올 수 있습니다.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs 소비자 그룹과 Kafka 소비자 그룹 비교
Event Hubs에서 이벤트 허브 소비자 그룹과 Kafka 소비자 그룹 간의 차이점은 무엇 인가요? Event Hubs의 kafka 소비자 그룹은 표준 Event Hubs 소비자 그룹과 완전히 다릅니다.

**Event Hubs 소비자 그룹**

- 포털, SDK 또는 Azure Resource Manager 템플릿을 통해 CRUD (만들기, 검색, 업데이트 및 삭제) 작업을 통해 관리 됩니다. Event Hubs 소비자 그룹을 자동으로 만들 수 없습니다.
- 이벤트 허브의 자식 엔터티입니다. 즉, 동일한 소비자 그룹 이름이 개별 엔터티가 기 때문에 동일한 네임 스페이스의 이벤트 허브 간에 다시 사용할 수 있습니다.
- 오프셋을 저장 하는 데 사용 되지 않습니다. 오케스트레이션 AMQP 소비는 Azure Storage와 같은 외부 오프셋 저장소를 사용 하 여 수행 됩니다.

**Kafka 소비자 그룹**

- 자동으로 생성 됩니다.  Kafka 그룹은 Kafka 소비자 그룹 Api를 통해 관리할 수 있습니다.
- Event Hubs 서비스에 오프셋을 저장할 수 있습니다.
- 이는 실제로 오프셋 키-값 저장소의 키로 사용 됩니다. 및의 고유 쌍의 `group.id` 경우 `topic-partition` 오프셋을 Azure Storage (3 x 복제)로 저장 합니다. Event Hubs 사용자는 Kafka 오프셋을 저장할 때 추가 저장소 비용이 발생 하지 않습니다. 오프셋은 Kafka 소비자 그룹 Api를 통해 manipulable, 오프셋 저장소 *계정은* 이벤트 허브 사용자에 게 직접 표시 되거나 manipulable 되지 않습니다.  
- 네임 스페이스를 확장 합니다. 여러 항목에서 여러 응용 프로그램에 동일한 Kafka 그룹 이름을 사용 하는 것은 단일 응용 프로그램에만 균형을 다시 조정 해야 할 때마다 모든 응용 프로그램 및 해당 Kafka 클라이언트를 균형는 것을 의미 합니다.  그룹 이름을 신중 하 게 선택 합니다.
- Event Hubs 소비자 그룹과 완전히 다릅니다. ' $Default '를 사용할 필요가 없으며 AMQP 워크 로드를 방해 하는 Kafka 클라이언트에 대해서도 걱정할 **필요가 없습니다.**
- Azure Portal에서 볼 수 없습니다. 소비자 그룹 정보는 Kafka Api를 통해 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [Event Hubs에 대 한 Apache Kafka 개발자 가이드](apache-kafka-developer-guide.md)
- [Event Hubs에 대 한 Apache Kafka 마이그레이션 가이드](apache-kafka-migration-guide.md)
- [Event Hubs에 대 한 Apache Kafka 문제 해결 가이드](apache-kafka-troubleshooting-guide.md)
- [권장 구성](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

