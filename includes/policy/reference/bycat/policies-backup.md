---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/16/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ddb6402e4250ca9b361bcb40a393c125d271fdf6
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982103"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup을 사용하도록 설정하여 Azure Virtual Machines를 보호하세요. Azure Backup은 Azure를 위한 안전하고 경제적인 데이터 보호 솔루션입니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[특정 위치에 있는 VM을 동일한 위치에 있는 기존 중앙 자격 증명 모음으로 백업하는 작업 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |이 정책은 지정된 위치에 있는 VM에 대한 Azure Backup 보호를 동일한 위치에 있는 기존 중앙 자격 증명 모음으로 구성합니다. 이 정책은 아직 백업이 구성되지 않은 VM에만 적용됩니다. 이 정책은 200개 이하의 VM에 할당하는 것이 좋습니다. 이 정책이 200개가 넘는 VM에 할당될 경우 정의된 스케줄보다 몇 시간 뒤에 백업이 트리거될 수 있습니다. 이 정책은 더 많은 VM 이미지를 지원할 수 있도록 향상될 예정입니다. |deployIfNotExists, auditIfNotExists, disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[Recovery Services 자격 증명 모음에 대한 진단 설정을 리소스별 범주의 Log Analytics 작업 영역에 배포합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |Recovery Services 자격 증명 모음에 대한 진단 설정을 배포하여 리소스별 범주의 Log Analytics 작업 영역으로 스트리밍합니다. 리소스별 범주가 활성화되지 않은 경우 새 진단 설정이 만들어집니다. |deployIfNotExists |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
