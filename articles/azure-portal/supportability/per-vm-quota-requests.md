---
title: Azure VM 시리즈당 vCPU 할당량 한도 증가 요청
description: Azure 포털에서 VM 시리즈에 대한 vCPU 할당량 제한을 늘리도록 요청하는 방법, 이는 총 지역 vCPU 제한을 동일한 양만큼 증가시킵니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843744"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>표준 할당량: VM 시리즈별 제한 증가

Azure 리소스 관리자는 가상 시스템에 대한 두 가지 유형의 vCPU 할당량을 지원합니다.

* *종량제 VM* 및 *예약된 VM 인스턴스에는* *표준 vCPU 할당량이*적용됩니다.
* *스팟 VM에는* *스팟 vCPU 할당량이*적용됩니다.

종량제 및 예약된 가상 시스템 인스턴스에 대한 표준 vCPU 할당량은 각 리전의 각 구독에 대해 두 계층으로 적용됩니다.

* 첫 번째 계층은 모든 VM 시리즈에서 *총 지역 vCPU 제한입니다.*
* 두 번째 계층은 Dv3 *시리즈 vCPU와 같은 VM 시리즈별 vCPU 제한입니다.*

새 스팟 VM을 배포할 때마다 해당 VM 시리즈의 총 신규 및 기존 vCPU 사용량이 특정 VM 계열에 대해 승인된 vCPU 할당량을 초과해서는 안 됩니다. 또한 모든 VM 시리즈에 배포되는 새 vCPU 및 기존 vCPU의 총 수는 구독에 대해 승인된 총 지역 vCPU 할당량을 초과해서는 안 됩니다. 이러한 할당량 중 하나를 초과하면 VM 배포가 허용되지 않습니다.

Azure 포털을 사용하여 VM 계열에 대한 vCPU 할당량 제한을 늘릴 것을 요청할 수 있습니다. VM 계열 할당량이 증가하면 전체 지역 vCPU 제한이 동일한 양만큼 자동으로 증가합니다.

표준 vCPU 할당량에 대한 자세한 내용은 [가상 컴퓨터 vCPU 할당량](../../virtual-machines/windows/quotas.md) 및 Azure 구독 및 서비스 [제한을](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)참조하십시오.

표준 할당량에 대한 지역별 vCPU 제한을 늘리는 방법에 대해 알아보려면 [표준 할당량: 지역별 제한 증가](regional-quota-requests.md)를 참조하십시오.

스팟 VM vCPU 제한을 늘리는 방법에 대한 자세한 내용은 [스팟 할당량: 모든 VM 계열에 대한 제한 증가를](low-priority-quota.md)참조하십시오.

다음 섹션에서 설명한 대로 두 가지 방법으로 VM 시리즈당 표준 vCPU 할당량 제한을 늘릴 것을 요청할 수 있습니다.

## <a name="request-a-standard-quota-increase-from-help--support"></a>도움말 + 지원에서 표준 할당량 증가 요청

**도움말 + 지원에서**VM 시리즈당 표준 vCPU 할당량 증가를 요청하려면 다음을 수행합니다.

> [!NOTE]
> 단일 지원 사례를 통해 여러 지역에 대한 할당량 제한 증가를 요청할 수도 있습니다. 자세한 내용은 8단계를 참조하십시오.

1. Azure [포털](https://portal.azure.com) 메뉴에서 **도움말 + 지원을**선택합니다.

   ![도움말 + 지원 링크](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **도움말 + 지원에서** **새 지원 요청을**선택합니다.

    ![새 지원 요청 만들기](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **구독의**경우 할당량을 늘리려는 구독을 선택합니다.

   ![증가된 할당량에 대한 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형의**경우 **컴퓨트 VM(코어-vCPU) 구독 한도 증가를 선택합니다.**

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음:** **문제 세부 정보를**여는 해결 방법을 선택합니다. 추가 정보를 입력하려면 **세부 정보 제공을** 선택합니다.

   !["세부 정보 제공" 링크](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 할당량 **세부 정보에서**다음 단계를 수행합니다.

   ![T추가 할당량 세부 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. **배포 모델의**경우 적절한 모델을 선택합니다.

   1. **위치의**경우 위치를 선택합니다. 선택한 위치에 대해 **유형** **선택에서**유형 아래에서 **표준**을 선택합니다.

      ![할당량 세부 정보 - 할당량 유형](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **유형에서**다중 선택 지원을 통해 단일 지원 사례에서 표준 및 스팟 할당량 유형을 모두 요청할 수 있습니다.

      스팟 할당량 제한을 늘리는 것에 대한 자세한 내용은 [가상 시스템 크기 집합에 대한 Azure 스팟 VM을](../../virtual-machine-scale-sets/use-spot.md)참조하십시오.

   1. **표준에서**할당량 증가에 대한 SKU 계열을 선택합니다.

      ![할당량 세부 정보 - SKU 시리즈](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 이 구독에 대해 원하는 새 할당량 제한을 입력합니다. 목록에서 SKU를 제거하려면 SKU 옆의 확인란을 선택하거나 "X" 삭제 아이콘을 선택합니다.

      ![새 vCPU 제한 선택](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 두 개 이상의 위치에 대한 할당량 증가를 요청하려면 **위치에서**추가 위치를 선택한 다음 적절한 VM 유형을 선택합니다. 그런 다음 추가 위치에 적용되는 제한을 입력할 수 있습니다.

   ![할당량 세부 정보에서 추가 위치 지정](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **저장을** 선택하고 지원 요청을 계속 만듭니다.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>구독에서 표준 할당량 증가 요청

구독에서 VM 시리즈당 표준 vCPU 할당량 증가를 **요청하려면 다음을 수행합니다.**

> [!NOTE]
> 단일 지원 사례를 통해 여러 지역에 대한 할당량 제한 증가를 요청할 수도 있습니다. 자세한 내용은 7단계를 참조하십시오.

1. Azure [포털에서](https://portal.azure.com)구독을 검색하고 **선택합니다.**

   ![Azure 포털 검색의 구독](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘리려는 구독을 선택합니다.

   ![변경 내용을 선택할 구독](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 왼쪽 창에서 사용 **+ 할당량을 선택합니다.**

   !["사용량 + 할당량" 링크](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 상단에서 **증가 요청을**선택합니다.

   ![할당량을 늘리려면 선택](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형의**경우 **컴퓨트 VM(코어-vCPU) 구독 한도 증가를 선택합니다.**

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 할당량 **세부 정보에서**다음 단계를 수행합니다.

   1. **배포 모델의**경우 적절한 모델을 선택하고 **위치에**대해 위치를 선택합니다.

      ![할당량 세부 정보 제공](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 선택한 위치에 대해 **유형**아래에서 **유형 선택을**선택한 다음 **표준**을 선택합니다.

      ![표준 유형 선택](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      **유형에서**다중 선택 지원을 통해 단일 지원 사례에서 표준 및 스팟 할당량 유형을 모두 요청할 수 있습니다.

      스팟 할당량 제한을 늘리는 것에 대한 자세한 내용은 [가상 시스템 크기 집합에 대한 Azure 스팟 VM을](../../virtual-machine-scale-sets/use-spot.md)참조하십시오.

   1. **표준의**경우 할당량을 늘리려는 SKU 계열을 선택합니다.

      ![할당량 세부 정보 - SKU 시리즈](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 이 구독에 대해 원하는 새 할당량 제한을 입력합니다. 목록에서 SKU를 제거하려면 SKU 옆의 확인란을 선택 취소하거나 "X" 삭제 아이콘을 선택합니다.

      ![새 vCPU 제한 선택](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 두 개 이상의 위치에 대한 할당량 증가를 요청하려면 **위치에서**추가 위치를 선택한 다음 적절한 VM 유형을 선택합니다.

   이 단계는 이전 위치에 대해 선택한 SKU 계열을 미리 로드합니다. 추가 계열에 적용할 할당량 제한을 입력합니다.

   ![할당량 세부 정보에서 추가 위치 선택](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. **저장을** 선택하고 지원 요청을 계속 만듭니다.
