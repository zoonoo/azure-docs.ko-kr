---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fb5f6dbb410a15bf88fccc1ed3f9c3ca0f830c70
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "96296093"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Synapse 작업 영역은 고객이 관리 하는 키를 사용 하 여 미사용 데이터를 암호화 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |고객 관리 키를 사용 하 여 Azure Synapse 작업 영역에 저장 된 데이터의 미사용 암호화를 제어 합니다. 고객 관리 키는 서비스 관리 키를 사용 하 여 기본 암호화를 기반으로 두 번째 암호화 계층을 추가 하 여 이중 암호화를 제공 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Azure Synapse 작업 영역에서 IP 방화벽 규칙을 제거 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |모든 IP 방화벽 규칙을 제거 하면 Azure Synapse 작업 영역이 개인 끝점 에서만 액세스할 수 있도록 하 여 보안을 향상 시킵니다. 이 구성에서는 작업 영역에 대 한 공용 네트워크 액세스를 허용 하는 방화벽 규칙의 생성을 감사 합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[Azure Synapse 작업 영역에서 관리 되는 작업 영역 가상 네트워크를 사용 하도록 설정 해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |관리 작업 영역 가상 네트워크를 사용 하도록 설정 하면 작업 영역이 다른 작업 영역에서 네트워크로 격리 됩니다. 이 가상 네트워크에 배포 된 데이터 통합 및 Spark 리소스는 또한 Spark 작업에 대 한 사용자 수준 격리를 제공 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[Azure Synapse 작업 영역에서 개인 끝점 연결을 사용 하도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |개인 끝점을 Azure Synapse 작업 영역에 비공개로 연결 하도록 구성할 수 있습니다. Azure Synapse 작업 영역에 보안 통신 채널을 적용 하는 데 사용 됩니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Synapse 관리 되는 개인 끝점은 승인 된 Azure Active Directory 테 넌 트의 리소스에만 연결 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |승인 된 Azure Active Directory (Azure AD) 테 넌 트의 리소스에 대 한 연결만 허용 하 여 Synapse 작업 영역을 보호 합니다. 승인 된 Azure AD 테 넌 트는 정책 할당 중에 정의 될 수 있습니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
