---
title: 이동 오류 문제 해결
description: Azure Resource Manager를 사용하여 리소스를 새 리소스 그룹 또는 구독으로 이동합니다.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891269"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Azure 리소스를 새 리소스 그룹 또는 구독으로 이동 하는 문제 해결

이 문서에서는 리소스를 이동할 때 문제를 해결 하는 데 도움이 되는 제안을 제공 합니다.

## <a name="upgrade-a-subscription"></a>구독 업그레이드

실제로 Azure 구독을 업그레이드하려면(예: 평가판에서 종량제로 업그레이드) 구독을 변환해야 합니다.

* 평가판을 업그레이드하려면 [평가판 또는 Microsoft Imagine Azure 구독을 종량제로 업그레이드](../../billing/billing-upgrade-azure-subscription.md)를 참조하세요.
* 종량제 계정을 변경하려면 [다른 제안으로 Azure 종량제 구독 변경](../../billing/billing-how-to-switch-azure-offer.md)을 참조하세요.

구독을 변환할 수 없으면 [Azure 지원 요청을 만듭니다](../../azure-portal/supportability/how-to-create-azure-support-request.md). 문제 유형으로 **구독 관리**를 선택합니다.

## <a name="service-limitations"></a>서비스 제한 사항

일부 서비스의 경우 리소스를 이동할 때 추가로 고려해 야 할 사항이 있습니다. 다음 서비스를 이동 하는 경우 지침 및 제한 사항을 확인 해야 합니다.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [클래식 배포 모델](./move-limitations/classic-model-move-limitations.md)
* [네트워킹](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>대량 요청

가능한 경우 대용량 이동을 개별 이동 작업으로 나눕니다. 단일 작업에 800개가 넘는 리소스가 포함되면 Resource Manager가 즉시 오류를 반환합니다. 그러나 800개 미만의 리소스 이동도 시간 초과로 인해 실패할 수 있습니다.

## <a name="resource-not-in-succeeded-state"></a>리소스가 성공 상태 아님

리소스가 성공 상태에 있지 않아 이동할 수 없음을 나타내는 오류 메시지가 표시 되는 경우 실제로는 이동을 차단 하는 종속 리소스가 될 수 있습니다. 일반적으로 오류 코드는 **MoveCannotProceedWithResourcesNotInSucceededState**입니다.

원본 또는 대상 리소스 그룹에 가상 네트워크가 포함 된 경우 이동 하는 동안 가상 네트워크에 대 한 모든 종속 리소스의 상태를 확인 합니다. 이 검사에는 가상 네트워크에 직접적이 고 간접적으로 종속 된 리소스가 포함 되어 있습니다. 이러한 리소스 중 하나라도 실패 상태 이면 이동이 차단 됩니다. 예를 들어 가상 네트워크를 사용 하는 가상 머신이 실패 한 경우 이동이 차단 됩니다. 이동은 가상 머신이 이동 중인 리소스 중 하나가 아니며 이동에 대 한 리소스 그룹 중 하나가 아닌 경우에도 차단 됩니다.

이 오류가 표시 되 면 두 가지 옵션이 있습니다. 가상 네트워크가 없는 리소스 그룹으로 리소스를 이동 하거나 [지원 담당자에 게 문의 하세요](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.
