---
title: 가상 머신 할당량-Azure
description: Azure 사용 모델을 제공 하 여 Azure에서 필요에 따라 Vm을 제거 하도록 하는 데 드는 비용을 절감할 수 있도록 하는 Azure 사용에 대 한 할당량 제한을 늘립니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842795"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>스폿 할당량: 모든 VM 시리즈에 대 한 제한 증가

지점 Vm (가상 머신)은 다른 Azure 사용량 모델을 제공 합니다. Azure에서 종 량 제 또는 예약 된 VM 인스턴스 배포에 필요한 경우 Azure에서 가상 머신을 제거 하는 데 드는 비용을 절감할 수 있습니다. 지점 Vm에 대 한 자세한 내용은 [가상 머신 확장 집합에 대 한 Azure 스폿 vm](../../virtual-machine-scale-sets/use-spot.md)을 참조 하세요.

Azure Resource Manager는 가상 컴퓨터에 대 한 두 가지 유형의 vCPU 할당량을 지원 합니다.

* *종 량 제 vm* 및 *예약 vm 인스턴스* 는 *표준 vcpu 할당량*을 따릅니다.
* *지점 vm* 에는 *별색 vcpu 할당량이*적용 됩니다.

스폿 vCPU 할당량 유형에 대해 리소스 관리자 vCPU 할당량은 단일 지역 제한으로 사용 가능한 모든 가상 머신 시리즈에서 적용 됩니다.

새 스폿 VM을 배포할 때마다 모든 스폿 VM 인스턴스에 대 한 새로운 및 기존 vCPU 사용량은 모두 승인 된 지점 vCPU 할당량 제한을 초과 하면 안 됩니다. 스폿 할당량을 초과 하는 경우 스폿 VM 배포는 허용 되지 않습니다.

이 문서에서는 Azure Portal를 사용 하 여 스폿 vCPU 할당량 한도 증가를 요청 하는 방법을 설명 합니다.

표준 vCPU 할당량에 대 한 자세한 내용은 [가상 머신 vcpu 할당량](../../virtual-machines/windows/quotas.md) 및 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.

지역별 vCPU 제한을 늘리는 방법에 대 한 자세한 내용은 [표준 할당량: 지역별 제한 증가](regional-quota-requests.md)를 참조 하세요.

## <a name="request-a-quota-limit-increase-from-help--support"></a>도움말 + 지원에서 할당량 한도 증가 요청

**도움말 + 지원**을 사용 하 여 모든 가상 머신 시리즈에 대 한 별색 할당량 한도 증가를 요청 하려면:

> [!NOTE]
> 단일 지원 사례를 통해 여러 지역의 할당량 한도 증가를 요청할 수도 있습니다. 자세한 내용은 8 단계를 참조 하세요.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택 합니다.

   ![도움말 + 지원 링크](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **도움말 + 지원**에서 **새 지원 요청**을 선택 합니다.

    ![새 지원 요청 만들기](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **구독**의 경우 할당량을 늘릴 구독을 선택 합니다.

   ![증가 된 할당량에 대 한 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형**에서 **계산-VM (코어-vcpus) 구독 제한을 늘립니다**.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음: 솔루션** 을 선택 하 여 **문제 세부 정보**를 엽니다. **세부 정보 제공** 을 선택 하 여 추가 정보를 입력 합니다.

   !["세부 정보 제공" 링크](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 정보**에서 다음 단계를 수행 합니다.

   1. **배포 모델**에 대해 적절 한 모델을 선택 하 고 **위치에서 위치**를 선택 합니다.

      ![추가 할당량 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 선택한 위치에 대해 **유형 선택의 유형** **에서** **지점**을 선택 합니다.

      ![별색 유형 선택](./media/resource-manager-core-quotas-request/select-spot-type.png)

       **유형**에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 및 별색 할당량 유형을 모두 요청할 수 있습니다.

       자세한 내용은 [표준 할당량: VM 계열로 제한 증가](per-vm-quota-requests.md)를 참조 하세요.

   1. 이 구독에 대해 원하는 새 할당량 한도를 입력 합니다.

      ![지점 VM에 대 한 새 할당량을 선택 합니다.](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 두 개 이상의 위치에 대해 할당량 증가를 요청 하려면 **위치에서 추가**위치를 선택한 다음 적절 한 VM 유형을 선택 합니다. 그런 다음 추가 위치에 적용 되는 한도를 입력할 수 있습니다.

   ![할당량 세부 정보에 추가 위치 지정](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **저장을 선택 하 고 계속** 을 선택 하 여 지원 요청을 계속 만듭니다.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>구독 창에서 할당량 한도 증가 요청

**구독** 창에서 모든 VM 계열의 별색 할당량 한도 증가를 요청 하려면 다음을 수행 합니다.

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

1. **다음: 솔루션** 을 선택 하 여 **문제 세부 정보**를 엽니다. **세부 정보 제공** 을 선택 하 여 추가 정보를 입력 합니다. **할당량 정보**에 다음 정보를 입력 합니다.

   1. **배포 모델**에 대해 적절 한 모델을 선택 하 고 **위치에서 위치**를 선택 합니다.

      ![할당량 세부 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 선택한 위치에 대해 **유형 선택의 유형** **에서** **지점**을 선택 합니다.

      ![별색 유형 선택](./media/resource-manager-core-quotas-request/select-spot-type.png)

      자세한 내용은 [표준 할당량: VM 계열로 제한 증가](per-vm-quota-requests.md)를 참조 하세요.

   1. 이 구독에 대해 원하는 새 할당량 한도를 입력 합니다.

      ![VCPU 제한에 새 값을 입력 합니다.](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 두 개 이상의 위치에 대해 할당량 증가를 요청 하려면 **위치에서 추가**위치를 선택한 다음 적절 한 VM 유형을 선택 합니다. 그런 다음 추가 위치에 적용 되는 한도를 입력할 수 있습니다.

   ![할당량 정보에서 추가 위치를 선택 합니다.](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **저장을 선택 하 고 계속** 을 선택 하 여 지원 요청을 계속 만듭니다.
