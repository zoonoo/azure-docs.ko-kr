---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8081624a533925b5a9c513b540c4296ed16a346e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91821082"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SignalR Service는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |승인된 프라이빗 엔드포인트 연결이 하나 이상 없는 Azure SignalR Service 리소스를 감사합니다. 가상 네트워크의 클라이언트는 프라이빗 링크를 통해 프라이빗 엔드포인트 연결이 있는 리소스에 안전하게 액세스할 수 있습니다. 자세한 내용은 [https://aka.ms/asrs/privatelink](https://aka.ms/asrs/privatelink)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
