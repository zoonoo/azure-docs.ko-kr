---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cfa483ef99b0b82dd38409c39e8b367b0d931083
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039976"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |프라이빗 엔드포인트 연결은 Azure Database for PostgreSQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
