---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 33e9ef75bcb7403a9faf1638f23498e18d5e226c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100618"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |가상 네트워크가 구성되지 않은 VM Image Builder 템플릿을 감사합니다. 가상 네트워크가 구성되지 않으면 대신 공용 IP가 생성되고 사용됩니다. 그러면 인터넷에 직접 리소스가 노출되고 잠재적인 공격 표면이 늘어날 수 있습니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
