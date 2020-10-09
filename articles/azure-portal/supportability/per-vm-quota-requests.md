---
title: Azure VM 시리즈 당 vCPU 할당량 한도 증가 요청
description: Azure Portal에서 VM 계열의 vCPU 할당량 한도 증가를 요청 하는 방법입니다. 그러면 전체 지역 vCPU 한도가 같은 양만큼 증가 합니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e00b1cf11b906390335f257b20787d394b3a8ed4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85117168"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>표준 할당량: VM 시리즈별 제한 늘리기

Azure Resource Manager는 가상 컴퓨터에 대 한 두 가지 유형의 vCPU 할당량을 지원 합니다.

* *종 량 제 vm* 및 *예약 vm 인스턴스* 는 *표준 vcpu 할당량*을 따릅니다.
* *지점 vm* 에는 *별색 vcpu 할당량이*적용 됩니다.

종 량 제 및 예약 된 가상 머신 인스턴스에 대 한 표준 vCPU 할당량은 각 지역의 각 구독에 대해 두 개의 계층으로 적용 됩니다.

* 첫 번째 계층은 모든 VM 시리즈에서 *총 지역 vCPUs 제한*입니다.
* 두 번째 계층은 Dv3 시리즈 vCPUs와 같은 *VM 당 시리즈 vCPUs 한도*입니다.

새 스폿 VM을 배포할 때마다 모든 스폿 VM 인스턴스에 대 한 새로운 및 기존 vCPU 사용량은 모두 승인 된 지점 vCPU 할당량 제한을 초과 하면 안 됩니다. 스폿 할당량을 초과 하는 경우 스폿 VM 배포는 허용 되지 않습니다.

Azure Portal를 사용 하 여 VM 시리즈에 대 한 vCPU 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량을 늘리면 전체 지역 vCPU 한도가 같은 양만큼 자동으로 증가 합니다.

표준 vCPU 할당량에 대 한 자세한 내용은 [가상 머신 vcpu 할당량](../../virtual-machines/windows/quotas.md) 및 [Azure 구독 및 서비스 제한](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)을 참조 하세요.

표준 할당량에 대 한 지역별 vCPU 제한을 늘리는 방법에 대 한 자세한 내용은 [표준 할당량: 지역별 제한 증가](regional-quota-requests.md)를 참조 하세요.

지점 VM vCPU 한도를 늘리는 방법에 대 한 자세한 내용은 [스폿 quota: 모든 VM 시리즈에 대 한 제한 증가](low-priority-quota.md)를 참조 하세요.

다음 섹션에 설명 된 대로 두 가지 방법 중 하나로 VM 시리즈 당 표준 vCPU 할당량 한도를 늘릴 수 있습니다.

## <a name="request-a-standard-quota-increase-from-help--support"></a>도움말 + 지원에서 표준 할당량 증가를 요청 합니다.

**도움말 + 지원**에서 VM 시리즈 당 표준 vcpu 할당량 증가를 요청 하려면 다음을 수행 합니다.

> [!NOTE]
> 단일 지원 사례를 통해 여러 지역의 할당량 한도 증가를 요청할 수도 있습니다. 자세한 내용은 8 단계를 참조 하세요.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택합니다.

   ![도움말 + 지원 링크](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **도움말 + 지원**에서 **새 지원 요청**을 선택합니다.

    ![새 지원 요청 만들기](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제 유형**에서 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.

   ![문제 유형 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **구독**의 경우 할당량을 늘릴 구독을 선택 합니다.

   ![증가 된 할당량에 대 한 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형**에서 **계산-VM (코어-vcpus) 구독 제한을 늘립니다**.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음: 솔루션** 을 선택 하 여 **문제 세부 정보**를 엽니다. **세부 정보 제공** 을 선택 하 여 추가 정보를 입력 합니다.

   !["세부 정보 제공" 링크](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 세부 정보**에서 다음 단계를 수행 합니다.

   ![TProvide 추가 정보를 제공 합니다.](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. **배포 모델**에서 적절 한 모델을 선택 합니다.

   1. 위치 **에서 위치를 선택**합니다. 선택한 위치에 대해 **유형 선택의 유형** **에서** **표준**을 선택 합니다.

      ![할당량 정보-할당량 유형](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **유형**에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 및 별색 할당량 유형을 모두 요청할 수 있습니다.

      집중 할당량 한도에 대 한 자세한 내용은 [가상 머신 확장 집합에 대 한 Azure 스폿 vm](../../virtual-machine-scale-sets/use-spot.md)을 참조 하세요.

   1. **표준**에서 증가 된 할당량에 대 한 SKU 시리즈를 선택 합니다.

      ![할당량 정보-SKU 시리즈](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 이 구독에 대해 원하는 새 할당량 한도를 입력 합니다. 목록에서 SKU를 제거 하려면 SKU 옆의 확인란 선택을 취소 하거나 취소 "X" 아이콘을 선택 합니다.

      ![새 vCPU 제한 선택](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 두 개 이상의 위치에 대해 할당량 증가를 요청 하려면 **위치에서 추가**위치를 선택한 다음 적절 한 VM 유형을 선택 합니다. 그런 다음 추가 위치에 적용 되는 한도를 입력할 수 있습니다.

   ![할당량 세부 정보에 추가 위치 지정](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **저장을 선택 하 고 계속** 을 선택 하 여 지원 요청을 계속 만듭니다.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>구독에서 표준 할당량 증가를 요청 합니다.

**구독**에서 VM 시리즈 당 표준 vcpu 할당량 증가를 요청 하려면 다음을 수행 합니다.

> [!NOTE]
> 단일 지원 사례를 통해 여러 지역의 할당량 한도 증가를 요청할 수도 있습니다. 자세한 내용은 7 단계를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 **구독**을 검색 하 고 선택 합니다.

   ![Azure Portal 검색의 구독](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘릴 구독을 선택 합니다.

   ![변경을 위해 선택할 구독](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 왼쪽 창에서 **사용량 + 할당량**을 선택 합니다.

   !["사용량 + 할당량" 링크](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 상단에서 **요청 증가**를 선택 합니다.

   ![할당량을 늘리려면 선택 합니다.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형**에서 **계산-VM (코어-vcpus) 구독 제한을 늘립니다**.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **할당량 세부 정보**에서 다음 단계를 수행 합니다.

   1. **배포 모델**에 대해 적절 한 모델을 선택 하 고 **위치에서 위치**를 선택 합니다.

      ![할당량 세부 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 선택한 위치의 **형식**에서 **유형 선택**을 선택한 다음 **표준**을 선택 합니다.

      ![표준 유형 선택](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **유형**에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 및 별색 할당량 유형을 모두 요청할 수 있습니다.

      집중 할당량 한도에 대 한 자세한 내용은 [가상 머신 확장 집합에 대 한 Azure 스폿 vm](../../virtual-machine-scale-sets/use-spot.md)을 참조 하세요.

   1. **표준**의 경우 할당량을 증가 시킬 SKU 시리즈를 선택 합니다.

      ![할당량 정보-SKU 시리즈](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 이 구독에 대해 원하는 새 할당량 한도를 입력 합니다. 목록에서 SKU를 제거 하려면 SKU 옆의 확인란을 선택 취소 하거나 취소 "X" 아이콘을 선택 합니다.

      ![새 vCPU 제한 선택](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 두 개 이상의 위치에 대해 할당량 증가를 요청 하려면 **위치에서 추가**위치를 선택한 다음 적절 한 VM 유형을 선택 합니다.

   이 단계에서는 이전 위치에 대해 선택한 SKU 시리즈를 미리 로드 합니다. 추가 시리즈에 적용 하려는 할당량 한도를 입력 합니다.

   ![할당량 정보에서 추가 위치를 선택 합니다.](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **저장을 선택 하 고 계속** 을 선택 하 여 지원 요청을 계속 만듭니다.
