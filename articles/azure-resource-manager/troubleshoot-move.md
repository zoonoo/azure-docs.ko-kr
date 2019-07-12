---
title: 새 구독 또는 리소스 그룹에 Azure 리소스를 이동할 때 오류 문제 해결
description: Azure Resource Manager를 사용하여 리소스를 새 리소스 그룹 또는 구독으로 이동합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723468"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>새 리소스 그룹 또는 구독으로 Azure 리소스를 이동 하는 문제 해결

이 문서에서는 리소스를 이동 하는 경우 문제를 해결 하는 데 도움이 되는 제안 사항을 제공 합니다.

## <a name="upgrade-a-subscription"></a>구독 업그레이드

실제로 Azure 구독을 업그레이드하려면(예: 평가판에서 종량제로 업그레이드) 구독을 변환해야 합니다.

* 평가판을 업그레이드하려면 [평가판 또는 Microsoft Imagine Azure 구독을 종량제로 업그레이드](../billing/billing-upgrade-azure-subscription.md)를 참조하세요.
* 종량제 계정을 변경하려면 [다른 제안으로 Azure 종량제 구독 변경](../billing/billing-how-to-switch-azure-offer.md)을 참조하세요.

구독을 변환할 수 없으면 [Azure 지원 요청을 만듭니다](../azure-supportability/how-to-create-azure-support-request.md). 문제 유형으로 **구독 관리**를 선택합니다.

## <a name="service-limitations"></a>서비스 제한 사항

일부 서비스 리소스를 이동할 때 추가적인 고려 사항이 필요 합니다. 다음 서비스를 이동 하는 경우 지침 및 제한 사항 확인 해야 합니다.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [클래식 배포 모델](./move-limitations/classic-model-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)
* [Virtual Network](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>큰 요청

가능한 경우 대용량 이동을 개별 이동 작업으로 나눕니다. 단일 작업에 800개가 넘는 리소스가 포함되면 Resource Manager가 즉시 오류를 반환합니다. 그러나 800개 미만의 리소스 이동도 시간 초과로 인해 실패할 수 있습니다.

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.
