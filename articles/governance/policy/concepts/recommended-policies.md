---
title: Azure 서비스에 대 한 권장 정책
description: Azure Virtual Machines와 같은 Azure 서비스에 권장 되는 정책을 찾고 적용 하는 방법을 설명 합니다.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 919d09a569fd950ab2061ba3452a4a940d25cb5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89447897"
---
# <a name="recommended-policies-for-azure-services"></a>Azure 서비스에 대 한 권장 정책

Azure Policy를 처음 접하는 고객은 리소스를 관리 하 고 관리 하는 일반적인 정책 정의를 찾는 경우가 종종 있습니다. Azure Policy **권장 정책은** 시작 하는 데 사용 되는 공통 정책 정의에 초점을 맞춘 목록을 제공 합니다. 지원 되는 리소스에 대 한 **권장 정책** 환경은 해당 리소스에 대 한 포털 환경에 포함 됩니다.

추가 Azure Policy 기본 제공 [Azure Policy 기본 제공 정의](../samples/built-in-policies.md)를 참조 하세요.

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

[Azure Virtual Machines](../../../virtual-machines/index.yml) 에 대 한 **권장 정책은** 가상 컴퓨터의 **개요** 페이지와 **기능** 탭 아래에 있습니다. _Azure Policy_ 카드에서 "구성 되지 않음" 또는 "# 할당" 텍스트를 선택 하 여 권장 되는 정책으로 측면 창을 엽니다. 가상 머신이 멤버인 범위에 이미 할당 된 모든 정책 정의는 회색으로 표시 됩니다. 이 가상 머신에 적용할 권장 정책을 선택 하 고 **정책 할당** 을 선택 하 여 각에 대 한 할당을 만듭니다.

조직에서 [리소스 및 리소스 계층 구조를 구성](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)하 여 완성도를 다 하는 경우 이러한 정책 할당을 리소스 당 하나에서 구독 또는 [관리 그룹](../../management-groups/index.yml) 수준으로 전환 하는 것이 좋습니다.

### <a name="azure-virtual-machines-recommended-policies"></a>Azure Virtual Machines 권장 정책

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[재해 복구가 구성되어 있지 않은 가상 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |재해 복구가 구성되지 않은 가상 머신을 감사합니다. 재해 복구에 대한 자세한 내용은 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)를 참조하세요. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[관리 디스크를 사용하지 않는 VM 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |이 정책은 관리 디스크를 사용하지 않는 VM을 감사합니다. |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |이 정책은 모든 가상 머신에 Azure Backup 서비스를 사용할 수 있는지 감사하는 데 도움이 됩니다. Azure Backup은 비용 효과적인 원클릭 백업 솔루션으로 데이터 복구를 간소화하며, 다른 클라우드 백업 서비스보다 사용하도록 설정하기가 쉽습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [정책 효과 이해](./effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.