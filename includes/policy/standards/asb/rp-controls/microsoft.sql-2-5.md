---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/28/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9a294bc71b8de4b442e7ca274636f4414100becd
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108211498"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정 대상에 대한 감사 기능이 있는 SQL Server는 보존 기간을 90일 이상으로 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |인시던트 조사를 위해 SQL Server 감사를 위한 데이터 보존 기간을 스토리지 계정 대상으로 90일 이상으로 설정하는 것이 좋습니다. 운영 중인 지역에 필요한 보존 규칙을 충족하는지 확인합니다. 이는 규정 표준을 준수해야 하는 경우도 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |
