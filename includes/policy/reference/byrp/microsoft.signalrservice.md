---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f6d1eeaada4f93ac19dafc0895d0faf3f53eac95
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050642"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR 서비스는 개인 링크를 사용 해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure 개인 링크를 사용 하면 원본 또는 대상에 공용 IP 주소를 사용 하지 않고 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. 개인 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리 합니다. 전체 서비스 대신 개인 끝점을 SignalR 리소스에 매핑하면 데이터 유출 위험 으로부터 보호 됩니다. 자세한 내용은을 (를) 확인 [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink) 하세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
