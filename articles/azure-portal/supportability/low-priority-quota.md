---
title: 스폿 가상 머신 할당량
description: 스폿 VM에 대한 할당량 한도를 늘립니다. 이것은 Azure에서 필요에 따라 VM을 제거하는 대신 비용을 절감할 수 있는 Azure 사용량 모델을 제공합니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c8b9a2251d7923fe1919b7b934f6c97877cd5b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745488"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>스폿 할당량: 모든 VM 시리즈에 대한 한도 늘리기

스폿 가상 머신(VMs)은 다른 Azure 사용량 모델을 제공합니다. 종량제 또는 예약된 VM 인스턴스 배포에 필요한 경우 Azure에서 가상 머신을 제거하는 대신 비용을 절감할 수 있습니다. 스폿 VM에 대한 자세한 내용은 [가상 머신 확장 집합에 대한 Azure 스폿 VM](../../virtual-machine-scale-sets/use-spot.md)을 참조하세요.

Azure Resource Manager는 가상 머신에 대해 두 가지 vCPU 할당량을 지원합니다.

* *종량제 VM* 및 *예약 VM 인스턴스* 는 *표준 vCPU 할당량* 이 적용됩니다.
* *스폿 VM* 은 *스폿 vCPU 할당량* 이 적용됩니다.

스폿 vCPU 할당량 유형의 경우 사용 가능한 모든 가상 머신 시리즈에서 리소스 관리자 vCPU 할당량이 단일 지역 한도로 적용됩니다.

새 스폿 VM을 배포할 때마다 모든 스폿 VM 인스턴스에 대한 기존 및 새로운 vCPU 사용량의 합계는 승인된 스폿 vCPU 할당량 한도를 초과해서는 안 됩니다. 스폿 할당량을 초과하는 경우 스폿 VM 배포가 허용되지 않습니다.

이 문서에서는 Azure Portal을 사용하여 스폿 vCPU 할당량 한도를 늘리는 방법에 대해 설명합니다.

표준 vCPU 할당량에 대한 자세한 내용은 [가상 머신 vCPU 할당량](../../virtual-machines/windows/quotas.md) 및 [Azure 구독과 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

vCPU 한도를 지역별로 늘리는 방법에 대한 자세한 내용은 [표준 할당량: 지역별 한도 증가](regional-quota-requests.md)를 참조하세요.

## <a name="request-a-quota-limit-increase-from-help--support"></a>도움말 + 지원에서 할당량 한도 증가 요청

**도움말 + 지원** 을 사용하여 모든 가상 머신 시리즈의 스폿 할당량 한도 증가를 요청하려면 다음을 수행합니다:

> [!NOTE]
> 또한 단일 지원 사례를 통해 여러 지역의 할당량 한도 증가를 요청할 수 있습니다. 자세한 내용은 8단계를 참조하세요.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원** 을 선택합니다.

   ![도움말 + 지원 링크](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **도움말 + 지원** 에서 **새 지원 요청** 을 선택합니다.

    ![새 지원 요청 만들기](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제점 유형** 에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **구독** 의 경우 할당량을 늘릴 구독을 선택하세요.

   ![늘어난 할당량 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형** 의 경우 **Compute VM(cores-vCPUs) 구독 제한이 증가** 를 선택합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음: 솔루션** 을 선택하여 **문제 세부 정보** 를 여세요. 추가 정보를 입력하려면 **상세 정보 제공** 을 선택하세요.

   !["세부 정보 제공" 링크](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 세부 정보** 의 경우 다음 단계를 수행하세요.

   1. **배포 모델** 의 경우 적절한 모델을 선택하고 **위치** 에 대해 위치를 선택하세요.

      ![추가 할당량 세부 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 선택한 위치의 경우 **유형**, **유형 선택** 에서 **스폿** 을 선택하세요.

      ![스폿 유형 선택](./media/resource-manager-core-quotas-request/select-spot-type.png)

       **유형** 에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 할당량 유형과 스폿 할당량 유형을 모두 요청할 수 있습니다.

       자세한 내용은 [ 표준 할당량: VM 시리즈의 제한 증가](per-vm-quota-requests.md)를 참조하세요.

   1. 이 구독에 대한 원하는 새 할당량 제한을 입력하세요.

      ![스폿 VM에 대한 새 할당량 선택](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 둘 이상의 위치에 대한 할당량 증가를 요청하려면 **위치** 에서 추가 위치를 선택한 다음 적절한 VM 유형을 선택하세요. 그런 다음 추가 위치에 적용되는 한도를 입력할 수 있습니다.

   ![할당량 세부 정보에 추가 위치 지정](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 지원 요청 생성을 계속하려면 **저장 후 계속** 을 선택하세요.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>구독 창에서 할당량 제한 증가 요청

**구독** 창에서 모든 VM 시리즈에 대한 스폿 할당량 한도 증가를 요청하려면 다음을 수행합니다.

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

1. **다음: 솔루션** 을 선택하여 **문제 세부 정보** 를 여세요. 추가 정보를 입력하려면 **상세 정보 제공** 을 선택하세요. **할당량 세부 정보** 에 다음 정보를 입력하세요.

   1. **배포 모델** 의 경우 적절한 모델을 선택하고 **위치** 에 대해 위치를 선택하세요.

      ![할당량 세부 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 선택한 위치의 경우 **유형**, **유형 선택** 에서 **스폿** 을 선택하세요.

      ![스폿 유형 선택](./media/resource-manager-core-quotas-request/select-spot-type.png)

      자세한 내용은 [ 표준 할당량: VM 시리즈의 제한 증가](per-vm-quota-requests.md)를 참조하세요.

   1. 이 구독에 대한 원하는 새 할당량 제한을 입력하세요.

      ![vCPU 제한에 대한 새 값 입력](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 둘 이상의 위치에 대한 할당량 증가를 요청하려면 **위치** 에서 추가 위치를 선택한 다음 적절한 VM 유형을 선택하세요. 그런 다음 추가 위치에 적용되는 한도를 입력할 수 있습니다.

   ![할당량 세부 정보에서 추가 위치 선택](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 지원 요청 생성을 계속하려면 **저장 후 계속** 을 선택하세요.
