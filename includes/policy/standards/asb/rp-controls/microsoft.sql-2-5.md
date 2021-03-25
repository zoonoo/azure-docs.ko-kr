---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0e6aaab50b549f0bf5c971c0e05c83b84a8b379b
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104792"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[저장소 계정 대상에 감사를 사용 하는 SQL server는 90 일 보존 이상으로 구성 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |인시던트 조사를 위해 SQL Server의 감사에 대 한 데이터 보존 기간을 저장소 계정 대상으로 90 이상으로 설정 하는 것이 좋습니다. 운영 중인 지역에 대해 필요한 보존 규칙을 충족 하 고 있는지 확인 합니다. 이는 규정 표준을 준수해야 하는 경우도 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
