---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: a1b12a72434034ecc6cbe527cc3de6454e4293a0
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79382165"
---
|속성 |Description |효과 |버전 |GitHub |
|---|---|---|---|---|
|[컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |CMK(고객 관리형 키)로 암호화가 설정되지 않은 감사 컨테이너 레지스트리입니다. CMK 암호화에 대한 자세한 내용은 https://aka.ms/acr/CMK 를 방문하세요. |감사, 사용 안 함 |1.0.0-preview |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |네트워크(IP 또는 VNET) 규칙이 구성되지 않은 감사 컨테이너 레지스트리는 기본적으로 모든 네트워크 액세스를 허용합니다. 하나 이상의 IP/방화벽 규칙 또는 구성된 가상 네트워크가 있는 컨테이너 레지스트리는 규정을 준수하는 것으로 간주됩니다. Container Registry 네트워크 규칙에 대한 자세한 내용은 https://aka.ms/acr/vnet 을 참조하세요. |감사, 사용 안 함 |1.0.0-preview |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)
