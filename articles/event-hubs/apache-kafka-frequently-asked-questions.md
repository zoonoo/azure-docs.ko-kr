---
title: 자주 묻는 질문 - 아파치 카프카를 위한 Azure 이벤트 허브
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
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606744"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>자주 묻는 질문 - 아파치 카프카의 이벤트 허브 
이 문서에서는 아파치 카프카의 이벤트 허브로 마이그레이션에 자주 묻는 몇 가지 질문에 대한 답변을 제공합니다.

## <a name="do-you-run-apache-kafka"></a>당신은 아파치 카프카를 실행합니까?

아니요.  이벤트 허브 인프라에 대해 Kafka API 작업을 실행합니다.  아파치 카프카와 이벤트 허브 AMQP 기능(즉, 생산, 수신, 관리 등)과 긴밀한 상관 관계가 있기 때문에 이벤트 허브의 알려진 안정성을 Kafka PaaS 공간에 가져올 수 있습니다.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>이벤트 허브 소비자 그룹 vs. 카프카 소비자 그룹
이벤트 허브 소비자 그룹과 이벤트 허브의 카프카 소비자 그룹의 차이점은 무엇입니까? 이벤트 허브의 카프카 소비자 그룹은 표준 이벤트 허브 소비자 그룹과 완전히 구별됩니다.

**이벤트 허브 소비자 그룹**

- 포털, SDK 또는 Azure 리소스 관리자 템플릿을 통해 CRUD(만들기, 검색, 업데이트 및 삭제) 작업을 사용하여 관리됩니다. 이벤트 허브 소비자 그룹은 자동으로 만들 수 없습니다.
- 이벤트 허브의 자식 엔터티입니다. 즉, 동일한 네임스페이스의 이벤트 허브 간에 동일한 소비자 그룹 이름을 다시 사용할 수 있습니다.
- 오프셋을 저장하는 데 사용되지 않습니다. 오케스트레이션된 AMQP 소비는 외부 오프셋 저장소(예: Azure Storage)를 사용하여 수행됩니다.

**카프카 소비자 그룹**

- 자동으로 만들어집니다.  카프카 그룹은 카프카 소비자 그룹 API를 통해 관리할 수 있습니다.
- 이벤트 허브 서비스에 오프셋을 저장할 수 있습니다.
- 오프셋 키-값 저장소에서 키로 사용 됩니다. 고유 쌍의 `group.id` 경우 `topic-partition`Azure 저장소(3배 복제)에 오프셋을 저장합니다. 이벤트 허브 사용자는 Kafka 오프셋을 저장하여 추가 저장소 비용을 발생하지 않습니다. 오프셋은 Kafka 소비자 그룹 API를 통해 사용할 수 있지만 오프셋 저장소 *계정은* 이벤트 허브 사용자에게 직접 표시되거나 매니펄로 사용할 수 없습니다.  
- 네임스페이스에 걸쳐 있습니다. 여러 항목에서 여러 응용 프로그램에 대해 동일한 Kafka 그룹 이름을 사용하면 단일 응용 프로그램만 재조정해야 할 때마다 모든 응용 프로그램과 Kafka 클라이언트의 균형이 재조정됩니다.  그룹 이름을 현명하게 선택하십시오.
- 이벤트 허브 소비자 그룹과 완전히 구별됩니다. '$Default'을 사용할 **필요가** 없으며 AMQP 워크로드를 방해하는 Kafka 클라이언트에 대해 걱정할 필요가 없습니다.
- Azure 포털에서는 볼 수 없습니다. 소비자 그룹 정보는 Kafka API를 통해 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Event Hubs 및 Kafka용 Event Hubs에 대해 자세한 내용은 다음 문서를 참조하세요.  

- [이벤트 허브에 대한 아파치 카프카 개발자 가이드](apache-kafka-developer-guide.md)
- [이벤트 허브에 대한 아파치 카프카 마이그레이션 가이드](apache-kafka-migration-guide.md)
- [이벤트 허브에 대한 아파치 카프카 문제 해결 가이드](apache-kafka-troubleshooting-guide.md)
- [권장 구성](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

