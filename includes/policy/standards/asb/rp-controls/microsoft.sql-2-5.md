---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 564c07c3a59e13dda5fbbc992855d5ca6019dad6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586813"
---
|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL server는 최소 90 일 동안 감사 데이터를 보존 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |인시던트 조사를 위해 SQL server의 감사 데이터에 대 한 데이터 보존 기간을 최소 90 일로 설정 하는 것이 좋습니다. 운영 중인 지역에 대해 필요한 보존 규칙을 충족 하 고 있는지 확인 합니다. 이는 규정 표준을 준수 해야 하는 경우도 있습니다. |AuditIfNotExists, 사용 안 함 |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
