---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a0751e7e4c90f04cd5e28610e2c62dbec6a7adcf
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051203"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Synapse 작업 영역은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |고객 관리형 키를 사용하여 Azure Synapse 작업 영역에 저장된 데이터의 저장 데이터 암호화를 제어합니다. 고객 관리형 키는 서비스 관리형 키를 사용하여 수행되는 기본 암호화 위에 두 번째 암호화 계층을 추가하여 이중 암호화를 제공합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Azure Synapse 작업 영역에서 IP 방화벽 규칙을 제거해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |모든 IP 방화벽 규칙을 제거하면 프라이빗 엔드포인트에서만 Azure Synapse 작업 영역에 액세스할 수 있도록 하여 보안을 향상시킵니다. 이 구성에서는 작업 영역에서 공용 네트워크 액세스를 허용하는 방화벽 규칙 생성을 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[Azure Synapse 작업 영역에서 관리형 작업 영역 가상 네트워크를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |관리형 작업 영역 가상 네트워크를 사용하도록 설정하면 작업 영역이 다른 작업 영역과 격리된 네트워크가 됩니다. 이 가상 네트워크에 배포된 데이터 통합 및 Spark 리소스는 Spark 작업에 대한 사용자 수준 격리도 제공합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[Azure Synapse 작업 영역에서 프라이빗 엔드포인트 연결을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |프라이빗 엔드포인트를 Azure Synapse 작업 영역에 비공개로 연결하도록 구성할 수 있습니다. 이는 Azure Synapse 작업 영역에 보안 통신 채널을 적용하는 데 사용됩니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Synapse 관리형 프라이빗 엔드포인트는 승인된 Azure Active Directory 테넌트의 리소스에만 연결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |승인된 Azure AD(Azure Active Directory) 테넌트의 리소스에 대한 연결만 허용하여 Synapse 작업 영역을 보호합니다. 승인된 Azure AD 테넌트는 정책 할당 중에 정의할 수 있습니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
