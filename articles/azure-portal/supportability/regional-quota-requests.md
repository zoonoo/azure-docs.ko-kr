---
title: Azure 지역 vCPU 할당량 한도 증가 요청
description: Azure Portal 지역에 대 한 vCPU 할당량 한도 증가를 요청 하는 방법입니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76843687"
---
# <a name="standard-quota-increase-limits-by-region"></a>표준 할당량: 지역별 제한을 늘립니다.

Azure Resource Manager는 가상 컴퓨터에 대 한 두 가지 유형의 vCPU 할당량을 지원 합니다.

* *종 량 제 vm* 및 *예약 vm 인스턴스* 는 *표준 vcpu 할당량*을 따릅니다.
* *지점 vm* 에는 *별색 vcpu 할당량이*적용 됩니다.

종 량 제 및 예약 된 가상 머신 인스턴스에 대 한 표준 vCPU 할당량은 각 지역의 각 구독에 대해 두 개의 계층으로 적용 됩니다.

* 첫 번째 계층은 모든 VM 시리즈에서 *총 지역 vCPUs 제한*입니다.
* 두 번째 계층은 D 시리즈 vCPUs와 같은 *VM 당 시리즈 vcpus 제한*입니다.

새 스폿 VM을 배포할 때마다 해당 VM 시리즈에 대 한 새로운 새 및 기존 vCPU 사용량은 특정 VM 시리즈에 대해 승인 된 vCPU 할당량을 초과 하지 않아야 합니다. 또한 모든 VM 시리즈를 통해 배포 되는 새로운 및 기존 vCPUs의 총 수는 구독에 대해 승인 된 총 지역 Vcpus 할당량을 초과 하지 않아야 합니다. 이러한 할당량 중 하나가 초과 되 면 VM 배포는 허용 되지 않습니다.

Azure Portal를 사용 하 여 VM 시리즈에 대 한 vCPU 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량을 늘리면 전체 지역 vCPU 한도가 같은 양만큼 자동으로 증가 합니다.

새 구독을 만들 때 지역 vCPUs의 기본 총 수가 모든 개별 VM 시리즈에 대 한 총 기본 Vcpus 할당량과 같지 않을 수 있습니다. 이러한 차이로 인해 배포 하려는 각 개별 VM 시리즈에 대 한 충분 한 할당량이 있는 구독이 생성 될 수 있습니다. 그러나 모든 배포에 대 한 총 지역 vCPUs를 수용 하기에 충분 한 할당량이 없을 수 있습니다. 이 경우 지역 vCPUs의 총 수에 대 한 제한을 명시적으로 늘리려면 요청을 제출 해야 합니다. 총 지역 vCPU 제한은 해당 지역에 대 한 모든 VM 시리즈에서 승인 된 총 할당량을 초과할 수 없습니다.

표준 vCPU 할당량에 대 한 자세한 내용은 [가상 머신 vcpu 할당량](../../virtual-machines/windows/quotas.md) 및 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.

지점 VM vCPU 한도를 늘리는 방법에 대 한 자세한 내용은 [스폿 quota: 모든 VM 시리즈에 대 한 제한 증가](low-priority-quota.md)를 참조 하세요.

두 가지 방법 중 하나로 vCPU 표준 할당량 한도를 지역별로 늘릴 수 있습니다.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>도움말 + 지원에서 지역별 할당량 증가를 요청 합니다.

**도움말 + 지원**에서 지역별 vcpu 할당량 증가를 요청 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택 합니다.

   !["도움말 + 지원" 링크](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **도움말 + 지원**에서 **새 지원 요청**을 선택 합니다.

    ![새 지원 요청](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **구독**의 경우 할당량을 늘릴 구독을 선택 합니다.

   ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형**에서 **기타 요청**을 선택 합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **다음: 솔루션** 을 선택 하 여 **문제 세부 정보**를 엽니다. **설명**에 다음 정보를 제공 합니다.

    1. **배포 모델**에 **리소스 관리자**를 지정 합니다.  
    1. **지역**에 대해 **미국 동부 2**와 같이 필요한 지역을 지정 합니다.  
    1. **새 한도**에 대해 해당 지역에 대 한 새 vcpu 제한을 지정 합니다. 이 값은이 구독에 대 한 개별 SKU 계열의 승인 된 할당량 합계를 초과 해서는 안 됩니다.

    ![할당량 요청에 대 한 세부 정보 입력](./media/resource-manager-core-quotas-request/regional-details.png)

1. **검토 + 만들기** 를 선택 하 여 지원 요청을 계속 만듭니다.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>구독에서 지역별 할당량 증가를 요청 합니다.

**구독**에서 지역에 의해 vcpu 할당량 증가를 요청 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **구독**을 검색 하 고 선택 합니다.

   ![Azure Portal의 구독으로 이동](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘릴 구독을 선택 합니다.

   ![수정할 구독을 선택 합니다.](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 왼쪽 창에서 **사용량 + 할당량**을 선택 합니다.

   ![사용량 및 할당량 따르기 링크](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 상단에서 **요청 증가**를 선택 합니다.

   ![할당량을 늘리려면 선택 합니다.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형**에서 **기타 요청**을 선택 합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **다음: 솔루션** 을 선택 하 여 **문제 세부 정보**를 엽니다. **설명** 상자에 다음 추가 정보를 제공 합니다.

    1. **배포 모델**에 **리소스 관리자**를 지정 합니다.  
    1. **지역**에 대해 **미국 동부 2**와 같이 필요한 지역을 지정 합니다.  
    1. **새 한도**에 대해 해당 지역에 대 한 새 vcpu 제한을 지정 합니다. 이 값은이 구독에 대 한 개별 SKU 계열의 승인 된 할당량 합계를 초과 해서는 안 됩니다.

    ![세부 정보 입력](./media/resource-manager-core-quotas-request/regional-details.png)

1. **검토 + 만들기** 를 선택 하 여 지원 요청을 계속 만듭니다.
