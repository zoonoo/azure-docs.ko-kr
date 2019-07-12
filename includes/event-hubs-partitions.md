---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5d6d2285680884cf20daaeef0a1ade53afad5b90
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827702"
---
Event Hubs는 각 소비자만이 특정 하위 집합, 파티션 또는 메시지 스트림을 읽는 파티션된 소비자 패턴을 통해 메시지 스트리밍을 제공합니다. 이 패턴은 이벤트 처리를 위한 가로 눈금을 사용하며 큐 및 항목에 사용할 수 없는 기타 스트림 중심 기능을 제공합니다.

파티션은 Event Hub에서 보유하는 순서가 지정된 이벤트 시퀀스입니다. 최신 이벤트가 도착하면 이 시퀀스의 끝에 추가됩니다. 파티션을 "커밋 로그"로 생각할 수 있습니다.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Event Hubs는 Event Hub의 모든 파티션에 적용되도록 구성된 보존 시간에 대한 데이터를 유지합니다. 시간 단위로 이벤트가 만료됩니다. 명시적으로 삭제할 수 없습니다. 파티션은 독립적이며 자체 데이터 시퀀스를 포함하기 때문에 종종 다른 속도로 증가합니다.

![Event Hubs](./media/event-hubs-partitions/multiple_partitions.png)

파티션 수는 만들 때 지정되며 2와 32 사이여야 합니다. 파티션 수는 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해야 합니다. 파티션은 애플리케이션을 사용하는 데 필요한 다운스트림 병렬 처리와 관련된 데이터 구성 메커니즘입니다. Event Hub의 파티션 수는 예상되는 동시 판독기의 수와 직접적으로 관련이 있습니다. Event Hubs 팀에 문의하여 32개를 초과하는 파티션 수를 늘릴 수 있습니다.

파티션은 식별 가능하고 직접 보낼 수 있지만 파티션으로 직접 보내는 방법은 권장하지 않습니다. 대신에 도입 된 더 높은 수준의 구문을 사용할 수 있습니다 합니다 [이벤트 게시자](../articles/event-hubs/event-hubs-features.md#event-publishers) 섹션입니다. 

파티션은 이벤트 본문, 사용자 정의 속성 모음 및 파티션의 오프셋과 스트림 시퀀스의 번호와 같은 메타데이터를 포함하는 이벤트 데이터 시퀀스로 채워집니다.

최적의 크기를 달성 하려면 1:1 처리량 단위와 파티션 분산 하는 것이 좋습니다. 단일 파티션에 보장 된 ingress 및 egress 최대 1 개 처리량 단위는 합니다. 파티션의 처리량을 높이기 위해 할 수 있지만, 성능 보장 되지 않습니다. 이 때문에 이벤트 허브의 파티션 수가 되도록 보다 크거나 처리량 단위 수가 같은 것이 좋습니다.

필요 하려는 총 처리량을 지정 해야 하는 처리량 단위 수 및 최소 파티션 수를 하지만 파티션 수는 알 수 있을까요? 사용자가 원하는 다운스트림 병렬 처리 뿐만 아니라 향후 처리량 요구에 따라 파티션 수를 선택 합니다. 이벤트 허브 내에 있는 파티션 수에 대 한 요금은 없습니다.

파티션 및 가용성과 안정성 간의 균형에 대한 자세한 내용은 [Event Hubs 프로그래밍 가이드](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) 및 [Event Hubs의 가용성 및 일관성](../articles/event-hubs/event-hubs-availability-and-consistency.md) 문서를 참조하세요.
