---
title: Azure Cosmos DB 서비스 요청 시간 제한 예외 문제 해결
description: Cosmos DB 서비스 요청 시간 제한 예외를 진단 하 고 해결 하는 방법
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294273"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Azure Cosmos DB 요청 시간 제한 진단 및 문제 해결
Azure Cosmos DB HTTP 408 요청 시간 제한을 반환 했습니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 요청 시간 제한 예외에 대 한 알려진 원인과 해결 방법이 나와 있습니다.

### <a name="1-check-the-sla"></a>1. SLA를 확인 합니다.
고객이 [Azure Cosmos DB 모니터링](monitor-cosmos-db.md) 을 확인 하 여 408 예외가 Cosmos DB SLA를 위반 하는지 확인할 수 있습니다.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>해결 방법 1: Cosmos DB SLA를 위반 하지 않음
응용 프로그램은이 시나리오를 처리 하 고 일시적인 오류를 다시 시도해 야 합니다.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>해결 방법 2: Cosmos DB SLA를 위반 했습니다.
Azure 지원에 문의:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. 핫 파티션 키
Azure Cosmos DB는 전체 프로 비전 된 처리량을 실제 파티션에 균등 하 게 분산 합니다. 핫 파티션이 있으면 실제 파티션에 있는 하나 이상의 논리 파티션 키가 모든 물리적 파티션의 r o s/s를 사용 하는 반면, 다른 실제 파티션은 사용 하지 않는 상태가 됩니다. 증상으로, 사용 된 총 r u/초는 데이터베이스 또는 컨테이너에서 전체 프로 비전 된 r u/초 보다 작으므로 핫 논리 파티션 키에 대 한 요청에 대 한 제한 (429s)이 계속 표시 됩니다. 정규화 된 작업 부하 [메트릭을](monitor-normalized-request-units.md) 사용 하 여 작업에 핫 파티션이 발생 하는지 확인 합니다. 

#### <a name="solution"></a>해결책:
요청 볼륨과 저장소를 균등 하 게 분산 하는 좋은 파티션 키를 선택 합니다. [파티션 키를 변경](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/)하는 방법에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제 [진단 및 해결](troubleshoot-dot-net-sdk.md)
* [.Net V3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net V2](performance-tips.md) 에 대 한 성능 지침 알아보기