---
title: 확장성-Azure Event Hubs | Microsoft Docs
description: Azure Event Hubs의 크기를 조정 하는 방법에 대 한 정보를 제공 합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276783"
---
# <a name="scaling-with-event-hubs"></a>Event Hubs를 사용 하 여 크기 조정

Event Hubs를 사용 하 여 크기 조정에 영향을 줄 수 있는 두 가지 요인이 있습니다.
*   처리량 단위
*   파티션

## <a name="throughput-units"></a>처리량 단위

Event Hubs의 처리량 용량은 *처리량 단위*로 제어됩니다. 처리량 단위는 미리 구입한 용량의 단위입니다. 단일 처리량을 수 있습니다.

* 수신: 초당 최대 1MB 또는 초당 1,000회 이벤트(둘 중 빠른 쪽 적용)
* 송신: 초당 최대 2MB 또는 4096개의 이벤트

구입한 처리량 단위의 용량을 초과하면 수신이 제한되며 [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception)이 반환됩니다. 송신은 제한 예외를 생성하지 않지만 구입한 처리량 단위의 용량으로 제한됩니다. 게시 속도 예외를 수신하거나 더 높은 송신을 예상하는 경우 네임스페이스에 대해 구입한 처리량 단위의 수를 확인해야 합니다. [Azure Portal](https://portal.azure.com)에서 네임스페이스의 **크기 조정** 블레이드에서 처리량 단위를 관리할 수 있습니다. [Event Hubs API](event-hubs-api-overview.md)를 사용하여 프로그래밍 방식으로 처리량 단위를 관리할 수도 있습니다.

처리량 단위는 미리 구입하는 방식이며 시간당 요금이 청구됩니다. 구입하면, 처리량 단위는 최소 한시간으로 청구됩니다. Event Hubs 네임스페이스에 대해 최대 20개의 처리량 단위를 구입할 수 있으며, 네임스페이스의 모든 Event Hubs에서 공유할 수 있습니다.

Event Hubs의 **자동 확장** 기능은 필요한 사용량에 맞게 처리량 단위 수를 증가하여 자동으로 확장합니다. 처리량 단위를 늘리면 다음과 같은 상황에서 제한 시나리오를 예방할 수 있습니다.

- 데이터 수신 속도가 설정된 처리량 단위를 초과하는 경우.
- 데이터 송신 요청 속도가 설정된 처리량 단위를 초과하는 경우.

ServerBusy 오류로 인한 요청 실패 없이 부하가 최소 임계값을 초과하면 Event Hubs 서비스는 처리량을 높입니다. 

에 대 한 자세한 내용은 자동 팽창 기능을 참조 하십시오 [처리량 단위 자동으로](event-hubs-auto-inflate.md)입니다.

## <a name="partitions"></a>파티션

파티션을 통해 규모 확장 다운스트림 처리에 대 한 합니다. 파티션이 있는 Event Hubs에서 제공 하는 분할 된 소비자 모델을 인해 있습니다 스케일 아웃할 수 이벤트를 동시에 처리 하는 동안. 이벤트 허브에는 최대 32 개의 파티션이 있을 수 있습니다.

최적의 크기를 달성 하려면 1:1 처리량 단위와 파티션 분산 하는 것이 좋습니다. 단일 파티션에 보장 된의 수신 및 발신 최대 1 개 처리량 단위는 합니다. 파티션의 처리량을 높이기 위해 할 수 있지만, 성능 보장 되지 않습니다. 이 때문에 이벤트 허브의 파티션 수가 되도록 보다 크거나 처리량 단위 수가 같은 것이 좋습니다.

필요 하려는 총 처리량을 지정 해야 하는 처리량 단위 수 및 최소 파티션 수를 하지만 파티션 수는 알 수 있을까요? 사용자가 원하는 다운스트림 병렬 처리 뿐만 아니라 향후 처리량 요구에 따라 파티션 수를 선택 합니다. 이벤트 허브 내에 있는 파티션 수에 대 한 요금은 없습니다.

Event Hubs 가격에 대한 자세한 내용은 [Event Hubs 가격](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

- [처리량 단위 규모를 자동으로 조정](event-hubs-auto-inflate.md)
- [Event Hubs 서비스 개요](event-hubs-what-is-event-hubs.md)
