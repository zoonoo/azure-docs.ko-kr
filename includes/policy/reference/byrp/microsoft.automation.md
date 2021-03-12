---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d6ca440c8d6e3f0a61e231f2c661e6b5578f04ff
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614985"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Automation 계정 변수를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |중요한 데이터를 저장할 때 Automation 계정 변수 자산의 암호화를 사용하도록 설정해야 합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Automation 계정은 공용 네트워크 액세스를 사용 하지 않도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F955a914f-bf86-4f0e-acd5-e0766b0efcb6) |공용 네트워크 액세스를 사용 하지 않도록 설정 하면 리소스가 공용 인터넷에 노출 되지 않도록 하 여 보안이 향상 됩니다. 대신 전용 끝점을 만들어 Automation 계정 리소스의 노출을 제한할 수 있습니다. [https://docs.microsoft.com/azure/automation/how-to/private-link-security](https://docs.microsoft.com/azure/automation/how-to/private-link-security)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_PublicNetworkAccess_Audit.json) |
|[Azure Automation 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |고객 관리형 키를 사용하여 Azure Automation 계정의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키로 암호화되지만, 고객 관리형 키는 일반적으로 규정 준수 기준을 충족하는 데 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/automation-cmk](https://aka.ms/automation-cmk)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
|[공용 네트워크 액세스를 사용 하지 않도록 Azure Automation 계정 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23b36a7c-9d26-4288-a8fd-c1d2fa284d8c) |공용 인터넷을 통해 액세스할 수 없도록 Azure Automation 계정에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정 합니다. 이 구성은 데이터 누출 위험 으로부터 보호 하는 데 도움이 됩니다. 대신 전용 끝점을 만들어 Automation 계정 리소스의 노출을 제한할 수 있습니다. [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints)에서 자세히 알아보세요. |수정, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_PublicNetworkAccess_Modify.json) |
|[Azure Automation 계정에서 개인 끝점 연결 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0c3130e-7dda-4187-aed0-ee4a472eaa60) |개인 끝점 연결을 사용 하면 원본 또는 대상에 공용 IP 주소를 요구 하지 않고 Azure Automation 계정에 개인 연결을 사용 하 여 보안 통신을 수행할 수 있습니다. 에서 Azure Automation 개인 끝점에 대해 자세히 알아보세요 [https://docs.microsoft.com/azure/automation/how-to/private-link-security](https://docs.microsoft.com/azure/automation/how-to/private-link-security) . |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_PrivateEndpoints_DeployIfNotExist.json) |
|[Automation 계정에 대 한 개인 끝점 연결을 사용 하도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c2b3618-68a8-4034-a150-ff4abc873462) |개인 끝점 연결을 사용 하면 원본 또는 대상에서 공용 IP 주소가 없어도 Automation 계정에 대 한 개인 연결을 설정 하 여 보안 통신을 수행할 수 있습니다. 에서 Azure Automation 개인 끝점에 대 한 자세한 정보 [https://docs.microsoft.com/azure/automation/how-to/private-link-security](https://docs.microsoft.com/azure/automation/how-to/private-link-security) |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_PrivateEndpoint_AuditIfNotExist.json) |
