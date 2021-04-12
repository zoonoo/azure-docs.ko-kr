---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8ef6a5aaa936c3aa590ce9fb0e5efd939b0035cc
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097142"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |프라이빗 엔드포인트 연결은 Azure Database for MariaDB에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
