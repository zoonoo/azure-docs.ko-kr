---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/28/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c0ee52f22284db4c0fabc12ef49b52c879a97192
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108213083"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신에서 디스크 암호화를 적용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |디스크 암호화를 사용하도록 설정되지 않은 가상 머신은 추천 사항으로 Azure Security Center에서 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |
|[연결되지 않은 디스크는 암호화되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |이 정책은 암호화를 사용하지 않고 연결되지 않은 모든 디스크를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |
