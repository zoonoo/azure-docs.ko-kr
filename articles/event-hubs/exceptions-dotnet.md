---
title: Azure Event Hubs-.NET 예외
description: 이 문서에서는 Azure Event Hubs .NET 메시징 예외 및 제안 된 작업 목록을 제공 합니다.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: shvija
ms.openlocfilehash: 6a1d7c969d31033ae9d00d212cc8f1a45abbeda1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91347399"
---
# <a name="eventhubsexception---net"></a>EventHubsException-.NET
**EventHubsException** 는 서비스 내의 오류와 클라이언트에 관련 된 오류를 포함 하 여 Event Hubs 관련 된 작업에 문제가 발생 한 경우에 트리거됩니다. 

## <a name="exception-information"></a>예외 정보
예외에는 오류 컨텍스트 및 상대적 심각도를 이해 하는 데 도움이 되는 다음과 같은 컨텍스트 정보가 포함 됩니다. 

- **IsTransient**: 예외가 복구 가능한 것으로 간주 되는지 여부를 식별 합니다. 일시적인 것으로 간주 되는 경우에는 적절 한 재시도 정책이 이미 적용 되었으며 재시도에 실패 했습니다.
- **이유**: 근본 원인을 범주화 하 고 명확 하 게 설명 하는 데 도움이 되는 잘 알려진 오류의 집합을 제공 합니다. 이러한 이유는 예외 메시지의 텍스트를 검사할 때 예외 필터링 및 기타 논리를 적용 하는 것이 적합 하지 않을 수 있기 때문입니다. 몇 가지 주요 실패 이유는 다음과 같습니다.
    - **클라이언트 닫힘**: 이미 닫히거나 삭제 된 이벤트 허브 클라이언트를 사용할 때 발생 합니다. 응용 프로그램 코드를 확인 하 여 Event Hubs 클라이언트 라이브러리의 개체가 원하는 범위에서 만들어지고 닫히도록 확인 하는 것이 좋습니다.
    - **서비스 시간 제한**: Event Hubs 서비스가 예상 된 시간 내에 작업에 응답 하지 않았음을 나타냅니다. 이 문제는 일시적인 네트워크 문제 또는 서비스 문제로 인해 발생 했을 수 있습니다. Event Hubs 서비스에서 요청을 성공적으로 완료 하지 못할 수도 있습니다. 상태가 알려지지 않았습니다. 현재 상태를 확인 하 고 필요한 경우 다시 시도 하는 것이 좋습니다.
    - **할당량 초과**됨: 단일 소비자 그룹에 대 한 활성 읽기 작업이 너무 많습니다. 이 제한은 Event Hubs 네임 스페이스의 계층에 따라 달라 지 며 상위 계층으로 이동 하는 것이 필요할 수 있습니다. 또 다른 방법은 추가 소비자 그룹을 만들고 모든 그룹에 대 한 소비자 클라이언트 읽기 수가 한도 내에 있는지 확인 하는 것입니다. 자세한 내용은 [Azure Event Hubs 할당량 및 한도](event-hubs-quotas.md)를 참조 하세요.
    - **메시지 크기 초과**: 개별 이벤트와 이벤트 일괄 처리 모두에 허용 되는 최대 크기인 이벤트 데이터입니다. 이벤트의 데이터, 연결 된 메타 데이터 및 시스템 오버 헤드를 포함 합니다. 이 오류를 해결 하려면 일괄 처리로 전송 되는 이벤트의 수를 줄이거나 메시지에 포함 된 데이터의 크기를 줄이십시오. 크기 제한은 변경 될 수 있으므로 [Azure Event Hubs 할당량 및](event-hubs-quotas.md) 특정 항목에 대 한 제한을 참조 하세요.
    - **소비자 연결 끊김**: 이벤트 허브 인스턴스에서 이벤트 허브 서비스에 의해 소비자 클라이언트의 연결을 끊었습니다. 일반적으로 소유자 수준이 높은 소비자가 파티션 및 소비자 그룹 페어링에서 소유권을 어설션하는 경우에 발생 합니다.
    - **리소스를 찾을**수 없음: Event Hubs 서비스에서 이벤트 허브, 소비자 그룹, 파티션 등의 리소스를 찾을 수 없습니다. 삭제 되었거나 Event Hubs 서비스 자체에 문제가 있는 것일 수 있습니다.

## <a name="handling-exceptions"></a>예외 처리
여러 가지 방법으로 **EventHubException**  에 대 한 특정 실패 이유에 반응할 수 있습니다. 한 가지 방법은 catch 블록의 일부로 예외 필터 절을 적용 하는 것입니다.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>다음 단계
[레거시 문서](event-hubs-messaging-exceptions.md)에서 설명 하는 기타 예외도 있습니다. 그 중 일부는 레거시 Event Hubs .NET 클라이언트 라이브러리에만 적용 됩니다.