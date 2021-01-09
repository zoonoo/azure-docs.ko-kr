---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: abc714cfc5bd0978ae6947a95c8c07212adeb828
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049350"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |고객 관리 키를 사용 하 여 레지스트리의 미사용 내용에 대 한 암호화를 관리 합니다. 기본적으로 데이터는 서비스 관리 키를 사용 하 여 미사용으로 암호화 되지만, 고객 관리 키 (CMK)는 일반적으로 규정 준수 표준을 충족 하는 데 필요 합니다. CMKs를 사용 하면 사용자가 만들고 소유한 Azure Key Vault 키로 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. 에서 CMK 암호화에 대해 자세히 알아보세요 [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |기본적으로 Azure container registry는 네트워크의 호스트에서 인터넷을 통한 연결을 허용 합니다. 잠재적인 위협 으로부터 레지스트리를 보호 하려면 특정 공용 IP 주소 또는 주소 범위 에서만 액세스를 허용 합니다. 레지스트리에 IP/방화벽 규칙이 나 구성 된 가상 네트워크가 없으면 비정상 리소스에 표시 됩니다. 여기에서 Container Registry 네트워크 규칙에 대해 자세히 알아보세요 [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[컨테이너 레지스트리는 개인 링크를 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure 개인 링크를 사용 하면 원본 또는 대상에 공용 IP 주소를 사용 하지 않고 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. 개인 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리 합니다. 전체 서비스 대신 개인 끝점을 컨테이너 레지스트리에 매핑하면 데이터 유출 위험 으로부터 보호 됩니다. 자세한 내용은을 (를) 확인 [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) 하세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Azure Container Registry 이미지의 취약성을 수정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |컨테이너 이미지 취약성 평가는 레지스트리를 검사하여 푸시된 각 컨테이너 이미지의 보안 취약성을 확인하고 각 이미지에 대한 결과를 공개합니다(Qualys에서 제공). 취약성을 해결하면 컨테이너의 보안 상태가 크게 개선되며 공격으로부터 보호할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
