---
title: Azure VM 시리즈당 vCPU 할당량 한도 증가 요청
description: Azure Portal VM 시리즈의 vCPU 할당량 한도 증가를 요청하는 방법으로, 이렇게 하면 총 지역 vCPU 한도가 동일한 양만큼 증가합니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 5db3e538a64e275313e1e0ab01f6cc6350eabb77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745437"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>표준 할당량: VM 시리즈별 제한 늘리기 

Azure Resource Manager는 가상 머신에 대해 두 가지 vCPU 할당량을 지원합니다.

* ‘종량제 VM’ 및 ‘예약 VM 인스턴스’는 ‘표준 vCPU 할당량’이 적용됩니다.  
* ‘스폿 VM’은 ‘스폿 vCPU 할당량’이 적용됩니다. 

종량제 및 예약 가상 머신 인스턴스의 표준 vCPU 할당량은 각 지역의 구독마다 두 계층으로 적용됩니다.

* 첫 번째 계층은 모든 VM 시리즈의 ‘총 지역 vCPU 한도’입니다.
* 두 번째 계층은 Dv3 시리즈 vCPU와 같은 ‘VM 시리즈당 vCPU 한도’입니다.

새 스폿 VM을 배포할 때마다 모든 스폿 VM 인스턴스에 대한 기존 및 새로운 vCPU 사용량의 합계는 승인된 스폿 vCPU 할당량 한도를 초과해서는 안 됩니다. 스폿 할당량을 초과하는 경우 스폿 VM 배포가 허용되지 않습니다.

Azure Portal을 사용하여 VM 시리즈의 vCPU 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량이 증가하면 총 지역 vCPU 한도가 동일한 양만큼 자동으로 증가합니다.

표준 vCPU 할당량에 대한 자세한 내용은 [가상 머신 vCPU 할당량](../../virtual-machines/windows/quotas.md)과 [Azure 구독 및 서비스 한도](./classic-deployment-model-quota-increase-requests.md)를 참조하세요.

vCPU 표준 할당량 한도를 지역별로 늘리는 방법에 대한 자세한 내용은 [표준 할당량: 지역별 한도 증가](regional-quota-requests.md)를 참조하세요.

스폿 VM vCPU 한도를 늘리는 방법에 대한 자세한 내용은 [스폿 할당량: 모든 VM 시리즈에 대한 한도 늘리기](low-priority-quota.md)를 참조하세요.

다음 섹션에 설명된 두 가지 방법 중 하나로 VM 시리즈당 표준 vCPU 할당량 한도 증가를 요청할 수 있습니다.

## <a name="request-a-standard-quota-increase-from-help--support"></a>도움말 + 지원에서 표준 할당량 증가 요청

**도움말 + 지원** 에서 VM 시리즈당 표준 vCPU 할당량 증가를 요청하려면 다음을 수행합니다.

> [!NOTE]
> 또한 단일 지원 사례를 통해 여러 지역의 할당량 한도 증가를 요청할 수 있습니다. 자세한 내용은 8단계를 참조하세요.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원** 을 선택합니다.

   ![도움말 + 지원 링크](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **도움말 + 지원** 에서 **새 지원 요청** 을 선택합니다.

    ![새 지원 요청 만들기](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제 유형** 의 경우 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **구독** 의 경우 할당량을 늘릴 구독을 선택하세요.

   ![늘어난 할당량 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형** 의 경우 **Compute VM(cores-vCPUs) 구독 제한이 증가** 를 선택합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음: 솔루션** 을 선택하여 **문제 세부 정보** 를 여세요. 추가 정보를 입력하려면 **상세 정보 제공** 을 선택하세요.

   !["세부 정보 제공" 링크](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 세부 정보** 에서 다음 단계를 수행합니다.

   ![추가 할당량 세부 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. **배포 모델** 의 경우 적절한 모델을 선택합니다.

   1. **위치** 의 경우 위치를 선택합니다. 선택한 위치의 경우 **유형**, **유형 선택** 에서 **표준** 을 선택하세요.

      ![할당량 정보 - 할당량 유형](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **유형** 에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 할당량 유형과 스폿 할당량 유형을 모두 요청할 수 있습니다.

      스폿 할당량 한도에 대한 자세한 내용은 [가상 머신 확장 집합의 Azure 스폿 VM](../../virtual-machine-scale-sets/use-spot.md)을 참조하세요.

   1. **표준** 에서 증가된 할당량을 사용할 SKU 시리즈를 선택합니다.

      ![할당량 정보 - SKU 시리즈](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 이 구독에 대해 원하는 새 할당량 한도를 입력합니다. 목록에서 SKU를 제거하려면 SKU 옆의 확인란 선택을 취소하거나 무시 “X” 아이콘을 선택합니다.

      ![새 vCPU 한도 선택](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 둘 이상의 위치에 대한 할당량 증가를 요청하려면 **위치** 에서 추가 위치를 선택한 다음 적절한 VM 유형을 선택하세요. 그런 다음 추가 위치에 적용되는 한도를 입력할 수 있습니다.

   ![할당량 세부 정보에 추가 위치 지정](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 지원 요청 생성을 계속하려면 **저장 후 계속** 을 선택하세요.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>구독에서 표준 할당량 증가를 요청

**구독** 에서 VM 시리즈당 표준 vCPU 할당량 증가를 요청하려면 다음을 수행합니다.

> [!NOTE]
> 또한 단일 지원 사례를 통해 여러 지역의 할당량 한도 증가를 요청할 수 있습니다. 자세한 내용은 7단계를 참조하세요.

1. [ Azure Portal](https://portal.azure.com)에서 **구독** 을 검색하고 선택하세요.

   ![Azure Portal 검색 구독](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘릴 구독 등록을 선택하세요.

   ![변경 사항을 위해 선택할 구독](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 왼쪽 창에서 **사용량 + 할당량** 을 선택하세요.

   ![“사용량 + 할당량” 링크](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 상단에서  **증가 요청** 을 선택하세요.

   ![할당량 증가 선택](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형** 의 경우 **Compute VM(cores-vCPUs) 구독 제한이 증가** 를 선택합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **할당량 세부 정보** 에서 다음 단계를 수행합니다.

   1. **배포 모델** 의 경우 적절한 모델을 선택하고 **위치** 에 대해 위치를 선택합니다.

      ![할당량 세부 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 선택한 위치의 경우 **형식** 에서 **형식 선택** 을 선택한 다음, **표준** 을 선택합니다.

      ![표준 형식 선택](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **유형** 에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 할당량 유형과 스폿 할당량 유형을 모두 요청할 수 있습니다.

      스폿 할당량 한도에 대한 자세한 내용은 [가상 머신 확장 집합의 Azure 스폿 VM](../../virtual-machine-scale-sets/use-spot.md)을 참조하세요.

   1. **표준** 의 경우 할당량을 늘리려는 SKU 시리즈를 선택합니다.

      ![할당량 정보 - SKU 시리즈](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 이 구독에 대해 원하는 새 할당량 한도를 입력합니다. 목록에서 SKU를 제거하려면 SKU 옆의 확인란을 선택 취소하거나 무시 “X” 아이콘을 선택합니다.

      ![새 vCPU 한도 선택](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 둘 이상의 위치에 대한 할당량 증가를 요청하려면 **위치** 에서 추가 위치를 선택한 다음 적절한 VM 유형을 선택하세요.

   이 단계에서는 이전 위치에 대해 선택한 SKU 시리즈를 미리 로드합니다. 추가 시리즈에 적용할 할당량 한도를 입력합니다.

   ![할당량 세부 정보에서 추가 위치 선택](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 지원 요청 생성을 계속하려면 **저장 후 계속** 을 선택합니다.