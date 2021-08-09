---
title: Azure 지역 vCPU 할당량 한도 증가 요청
description: Azure Portal에서 지역의 vCPU 할당량 한도 증가를 요청하는 방법입니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745420"
---
# <a name="standard-quota-increase-limits-by-region"></a>표준 할당량: 지역별 한도 증가

Azure Resource Manager는 가상 머신에 대해 두 가지 vCPU 할당량을 지원합니다.

* ‘종량제 VM’ 및 ‘예약 VM 인스턴스’는 ‘표준 vCPU 할당량’이 적용됩니다.  
* ‘스폿 VM’은 ‘스폿 vCPU 할당량’이 적용됩니다. 

종량제 및 예약 가상 머신 인스턴스의 표준 vCPU 할당량은 각 지역의 구독마다 두 계층으로 적용됩니다.

* 첫 번째 계층은 모든 VM 시리즈의 ‘총 지역 vCPU 한도’입니다.
* 두 번째 계층은 D 시리즈 vCPU와 같은 ‘VM 시리즈당 vCPU 한도’입니다.

새 스폿 VM을 배포할 때마다 해당 VM 시리즈에 대한 신규 및 기존 vCPU 사용량은 특정 VM 시리즈에 대해 승인된 vCPU 할당량을 초과하지 않아야 합니다. 또한 모든 VM 시리즈를 통해 배포되는 신규 및 기존 vCPU의 합계는 구독에 대해 승인된 총 지역 vCPU 할당량을 초과하지 않아야 합니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다.

Azure Portal을 사용하여 VM 시리즈의 vCPU 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량이 증가하면 총 지역 vCPU 한도가 동일한 양만큼 자동으로 증가합니다.

새 구독을 만들 때 기본 총 지역 vCPU는 모든 개별 VM 시리즈의 총 기본 vCPU 할당량과 같지 않을 수 있습니다. 이러한 차이로 인해 배포하려는 개별 VM 시리즈에 대한 할당량이 충분한 구독이 생성될 수 있습니다. 그러나 모든 배포에 대한 총 지역 vCPU를 수용하기에는 할당량이 부족할 수 있습니다. 이 경우 총 지역 vCPU 한도를 명시적으로 늘리려면 요청을 제출해야 합니다. 총 지역 vCPU 한도는 해당 지역의 모든 VM 시리즈에서 총 승인된 할당량을 초과할 수 없습니다.

표준 vCPU 할당량에 대한 자세한 내용은 [가상 머신 vCPU 할당량](../../virtual-machines/windows/quotas.md) 및 [Azure 구독과 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

스폿 VM vCPU 한도를 늘리는 방법에 대한 자세한 내용은 [스폿 할당량: 모든 VM 시리즈에 대한 한도 늘리기](low-priority-quota.md)를 참조하세요.

두 가지 방법 중 하나를 통해 지역별 vCPU 표준 할당량 한도 증가를 요청할 수 있습니다.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>도움말 + 지원에서 지역별 할당량 증가 요청

**도움말 + 지원** 에서 지역별 vCPU 할당량 증가를 요청하려면:

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원** 을 선택합니다.

   ![“도움말 + 지원” 링크](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **도움말 + 지원** 에서 **새 지원 요청** 을 선택합니다.

    ![새 지원 요청](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제 유형** 의 경우 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **구독** 의 경우 할당량을 늘릴 구독을 선택합니다.

   ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형** 의 경우 **기타 요청** 을 선택합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **다음: 솔루션** 을 선택하여 **문제 세부 정보** 를 엽니다. **설명** 에서 다음 정보를 입력합니다.

    1. **배포 모델** 의 경우 **Resource Manager** 를 지정합니다.  
    1. **지역** 의 경우 필요한 지역(예: **미국 동부 2**)을 지정합니다.  
    1. **새 한도** 의 경우 지역의 새 vCPU 한도를 지정합니다. 이 값은 이 구독의 개별 SKU 시리즈에 대해 승인된 할당량의 합계를 초과해서는 안 됩니다.

    ![할당량 요청에 대한 세부 정보 입력](./media/resource-manager-core-quotas-request/regional-details.png)

1. **검토 + 만들기** 를 선택하여 지원 요청을 계속 만듭니다.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>구독에서 지역별 할당량 증가 요청

**구독** 에서 지역별 vCPU 할당량 증가를 요청하려면:

1. [Azure Portal](https://portal.azure.com)에서 **구독** 을 검색하고 선택합니다.

   ![Azure Portal에서 구독으로 이동](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘릴 구독을 선택합니다.

   ![수정할 구독을 선택합니다.](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 왼쪽 창에서 **사용량 + 할당량** 을 선택합니다.

   ![사용량 및 할당량 링크 따라가기](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 상단에서 **증가 요청** 을 선택합니다.

   ![할당량 증가 선택](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형** 에서 **기타 요청** 을 선택합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **다음: 솔루션** 을 선택하여 **문제 세부 정보** 를 엽니다. **설명** 상자에 다음 추가 정보를 제공합니다.

    1. **배포 모델** 의 경우 **Resource Manager** 를 지정합니다.  
    1. **지역** 의 경우 필요한 지역(예: **미국 동부 2**)을 지정합니다.  
    1. **새 한도** 의 경우 지역의 새 vCPU 한도를 지정합니다. 이 값은 이 구독의 개별 SKU 시리즈에 대해 승인된 할당량의 합계를 초과해서는 안 됩니다.

    ![세부 정보 입력](./media/resource-manager-core-quotas-request/regional-details.png)

1. **검토 + 만들기** 를 선택하여 지원 요청을 계속 만듭니다.
