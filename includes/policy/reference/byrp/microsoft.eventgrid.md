---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f98ff82ce09e7401c66cec6283544002a2b8bff0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99215236"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Event Grid 도메인은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스 대신 프라이빗 엔드포인트를 컨테이너 레지스트리에 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure Event Grid 토픽은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 토픽에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
