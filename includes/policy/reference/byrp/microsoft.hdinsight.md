---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 578cd451b53634bc63876e8ca2d25e3251eb8cc2
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556448"
---
|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure HDInsight 클러스터는 고객이 관리 하는 키를 사용 하 여 미사용 데이터를 암호화 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |CMK (고객이 관리 하는 키)를 사용 하 여 미사용 Azure HDInsight 클러스터의 암호화를 관리 합니다. 기본적으로 고객 데이터는 서비스 관리형 키로 암호화되지만, CMK는 일반적으로 규정 준수 기준을 충족하는 데 필요합니다. CMK를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키로 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk)에서 CMK 암호화에 대해 자세히 알아보세요.  |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight 클러스터는 호스트의 암호화를 사용 하 여 미사용 데이터를 암호화 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |호스트에서 암호화를 사용 하도록 설정 하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호할 수 있습니다. 호스트에서 암호화를 사용 하도록 설정 하면 VM 호스트에 저장 된 데이터는 미사용 및 저장소 서비스로 암호화 된 흐름으로 암호화 됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure HDInsight 클러스터는 전송 중인 암호화를 사용 하 여 Azure HDInsight 클러스터 노드 간 통신을 암호화 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |데이터는 Azure HDInsight 클러스터 노드 간 전송 중에 변조 될 수 있습니다. 전송 중 암호화를 사용 하도록 설정 하면이 전송 중 오용 및 변조 문제가 해결 됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
