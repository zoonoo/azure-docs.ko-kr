---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 38b083cf4843c3f10db22d83be9e01241d2266a4
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022095"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[컨테이너 레지스트리는 CMK (고객이 관리 하는 키)를 사용 하 여 암호화 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |CMK (고객이 관리 하는 키)를 사용 하 여 암호화가 설정 되지 않은 감사 컨테이너 레지스트리 Azure는 서비스 관리 키를 사용 하 여 미사용 레지스트리 콘텐츠를 자동으로 암호화 합니다. Azure Key Vault에서 만들고 관리하는 키를 사용하여 추가 암호화 계층을 통해 기본 암호화를 보완할 수 있습니다. CMK 암호화에 대한 자세한 내용은 [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[컨테이너 레지스트리는 무제한 네트워크 액세스를 허용 해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |네트워크 또는 방화벽 (IP) 규칙이 구성 되지 않은 감사 컨테이너 레지스트리는 기본적으로 모든 네트워크 액세스를 허용 합니다. 네트워크 액세스를 제한 하면 잠재적인 위협 으로부터 컨테이너 레지스트리를 보호할 수 있습니다. 하나 이상의 IP/방화벽 규칙 또는 구성 된 가상 네트워크가 있는 컨테이너 레지스트리는 규정을 준수 하는 것으로 간주 됩니다. 네트워크 규칙을 Container Registry 하는 방법에 대 한 자세한 내용은 및을 참조 하세요. [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[컨테이너 레지스트리는 개인 링크를 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |승인 된 개인 끝점 연결이 하나 이상 없는 컨테이너 레지스트리를 감사 합니다. 가상 네트워크의 클라이언트는 프라이빗 링크를 통해 프라이빗 엔드포인트 연결이 있는 리소스에 안전하게 액세스할 수 있습니다. 그러면 공용 액세스를 사용 하지 않도록 설정 하 여 레지스트리에 연결 하는 데 개인 링크만 사용할 수 있도록 할 수 있습니다. 자세한 내용은 [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Azure Container Registry 이미지의 취약성을 재구성 해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |컨테이너 이미지 취약점 평가는 푸시된 각 컨테이너 이미지의 보안 취약성에 대해 레지스트리를 검사 하 고 각 이미지에 대 한 자세한 결과를 표시 합니다 (Qualys에서 제공). 취약성을 해결하면 컨테이너의 보안 상태가 크게 개선되며 공격으로부터 보호할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
