---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 130ea1921bafa68e19a3d86710003d30c47dd8fd
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106419"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 데이터 팩터리는 고객이 관리 하는 키로 암호화 되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |고객 관리 키를 사용 하 여 미사용 Azure Data Factory의 암호화를 관리 합니다. 기본적으로 고객 데이터는 서비스 관리 키를 사용 하 여 암호화 되지만 고객 관리 키는 일반적으로 규정 준수 표준을 충족 하는 데 필요 합니다. 고객 관리 키를 사용 하면 사용자가 만들고 소유한 Azure Key Vault 키로 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/adf-cmk](https://aka.ms/adf-cmk)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
|[Azure Data Factory integration runtime에는 코어 수를 제한 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85bb39b5-2f66-49f8-9306-77da3ac5130f) |리소스 및 비용을 관리 하려면 통합 런타임에 대 한 코어 수를 제한 합니다. |감사, 거부, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/IR_Core_Count_Exceeds_Audit.json) |
|[Azure Data Factory 연결 된 서비스 리소스 종류가 허용 목록에 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6809a3d0-d354-42fb-b955-783d207c62a8) |Azure Data Factory 연결 된 서비스 유형의 허용 목록을 정의 합니다. 허용 되는 리소스 종류를 제한 하면 데이터 이동의 경계를 제어할 수 있습니다. 예를 들어 Data Lake Storage Gen1를 사용 하 여 blob 저장소를 허용 하거나 분석 또는 범위에 대해 Gen2를 허용 하 여 SQL 및 Kusto 실시간 쿼리에 액세스할 수만 있도록 범위를 제한 합니다. |감사, 거부, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/LinkedService_ResourceType_Audit.json) |
|[Azure Data Factory 연결 된 서비스는 암호 저장에 Key Vault를 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F127ef6d7-242f-43b3-9eef-947faf1725d0) |암호 (예: 연결 문자열)를 안전 하 게 관리 하려면 사용자가 연결 된 서비스에서 인라인으로 지정 하는 대신 Azure Key Vault를 사용 하 여 암호를 제공 해야 합니다. |감사, 거부, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/LinkedService_InlineSecrets_Audit.json) |
|[Azure Data Factory 연결 된 서비스는 지원 되는 경우 시스템 할당 관리 id 인증을 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff78ccdb4-7bf4-4106-8647-270491d2978a) |연결 된 서비스를 통해 데이터 저장소와 통신할 때 시스템 할당 관리 id를 사용 하면 암호나 연결 문자열과 같은 덜 안전한 자격 증명을 사용 하지 않아도 됩니다. |감사, 거부, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/LinkedService_All_Auth_Audit_except_MSI.json) |
|[소스 제어에 Git 리포지토리를 사용 해야 Azure Data Factory](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77d40665-3120-4348-b539-3192ec808307) |변경 내용 추적, 공동 작업, 연속 통합 및 배포와 같은 기능을 얻기 위해 데이터 팩터리에 대 한 소스 제어를 사용 하도록 설정 합니다. |감사, 거부, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/Factory_None_GIT_Audit.json) |
|[Azure Data Factory에서 공용 네트워크 액세스를 사용 하지 않도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cf164be-6819-4a50-b8fa-4bcaa4f98fb6) |공용 네트워크 액세스 속성을 사용 하지 않도록 설정 하면 개인 끝점 에서만 Azure Data Factory에 액세스할 수 있도록 하 여 보안을 향상 시킵니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PublicNetworkAccess_Audit.json) |
