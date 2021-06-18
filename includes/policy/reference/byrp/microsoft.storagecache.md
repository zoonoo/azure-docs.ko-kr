---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b19089e744d7ef6abf31e7fb71f1d0845a7255ca
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112041002"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache 계정은 암호화에 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |고객 관리형 키를 사용하여 Azure HPC Cache의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. |감사, 사용 안 함, 거부 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
