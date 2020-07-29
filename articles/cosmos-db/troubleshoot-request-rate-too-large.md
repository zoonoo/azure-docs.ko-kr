---
title: Azure Cosmos DB 요청 속도로 큼 예외 해결
description: 요청 률을 진단 하 고 크게 예외를 해결 하는 방법
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294270"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>너무 많은 요청 Cosmos DB 진단 및 문제 해결
' 요청 빈도 너무 큼 ' 또는 오류 코드 429은 요청을 제한 하 고 있음을 나타냅니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 너무 많은 요청에 대 한 알려진 원인 및 솔루션이 포함 되어 있습니다.

### <a name="1-check-the-metrics"></a>1. 메트릭을 확인 합니다.
고객은 [Azure Cosmos DB 모니터링](monitor-cosmos-db.md) 을 검사 하 여 숫자 429 예외가 있는지 확인 해야 합니다.

## <a name="cause"></a>원인:
사용 된 처리량 (r u/초)이 [프로 비전 된 처리량](set-throughput.md)을 초과 했습니다. SDK는 지정 된 재시도 정책에 따라 요청을 자동으로 다시 시도 합니다. 이 오류가 자주 발생 하는 경우 컬렉션에 대 한 처리량을 높이는 것이 좋습니다. 429 오류를 받고 있는지 확인 하려면 포털의 메트릭을 확인 하세요. 파티션 키를 검토 하 여 [저장소 및 요청 볼륨의 균등 한 배포가](partition-data.md)발생 하는지 확인 합니다.

## <a name="solution"></a>해결책:
1. 프로 비전 된 처리량을 늘리려면 [포털 또는 SDK](set-throughput.md) 를 사용 합니다.
2. [자동 크기 조정을](provision-throughput-autoscale.md)위해 데이터베이스 또는 컨테이너를 전환 합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제 [진단 및 해결](troubleshoot-dot-net-sdk.md)
* [.Net V3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net V2](performance-tips.md) 에 대 한 성능 지침 알아보기