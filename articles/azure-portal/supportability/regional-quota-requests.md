---
title: Azure 지역 vCPU 할당량 한도 증가 요청
description: Azure 포털의 리전에 대한 vCPU 할당량 제한 증가를 요청하는 방법
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843687"
---
# <a name="standard-quota-increase-limits-by-region"></a>표준 할당량: 지역별 한도 증가

Azure 리소스 관리자는 가상 시스템에 대한 두 가지 유형의 vCPU 할당량을 지원합니다.

* *종량제 VM* 및 *예약된 VM 인스턴스에는* *표준 vCPU 할당량이*적용됩니다.
* *스팟 VM에는* *스팟 vCPU 할당량이*적용됩니다.

종량제 및 예약된 가상 시스템 인스턴스에 대한 표준 vCPU 할당량은 각 리전의 각 구독에 대해 두 계층으로 적용됩니다.

* 첫 번째 계층은 모든 VM 시리즈에서 *총 지역 vCPU 제한입니다.*
* 두 번째 계층은 D 시리즈 *vCPU와 같은 VM 시리즈별 vCPU 제한입니다.*

새 스팟 VM을 배포할 때마다 해당 VM 시리즈의 총 신규 및 기존 vCPU 사용량이 특정 VM 계열에 대해 승인된 vCPU 할당량을 초과해서는 안 됩니다. 또한 모든 VM 시리즈에 배포되는 새 vCPU 및 기존 vCPU의 총 수는 구독에 대해 승인된 총 지역 vCPU 할당량을 초과해서는 안 됩니다. 이러한 할당량 중 하나를 초과하면 VM 배포가 허용되지 않습니다.

Azure 포털을 사용하여 VM 계열에 대한 vCPU 할당량 제한을 늘릴 것을 요청할 수 있습니다. VM 계열 할당량이 증가하면 전체 지역 vCPU 제한이 동일한 양만큼 자동으로 증가합니다.

새 구독을 만들 때 기본 지역 vCPU 수가 모든 개별 VM 시리즈의 총 기본 vCPU 할당량과 같지 않을 수 있습니다. 이러한 불일치로 인해 배포하려는 각 개별 VM 시리즈에 대해 충분한 할당량이 있는 구독이 발생할 수 있습니다. 그러나 모든 배포에 대한 전체 지역 vCPU를 수용하기에 충분한 할당량이 없을 수 있습니다. 이 경우 총 지역 vCPU 수 제한을 명시적으로 늘리는 요청을 제출해야 합니다. 총 지역 vCPU 한도는 리전의 모든 VM 계열에서 승인된 총 할당량을 초과할 수 없습니다.

표준 vCPU 할당량에 대한 자세한 내용은 [가상 컴퓨터 vCPU 할당량](../../virtual-machines/windows/quotas.md) 및 Azure 구독 및 서비스 [제한, 할당량 및 제약 조건을](../../azure-resource-manager/management/azure-subscription-service-limits.md)참조하십시오.

스팟 VM vCPU 제한을 늘리는 방법에 대한 자세한 내용은 [스팟 할당량: 모든 VM 계열에 대한 제한 증가를](low-priority-quota.md)참조하십시오.

두 가지 방법으로 지역별로 vCPU 표준 할당량 제한을 늘리를 요청할 수 있습니다.

## <a name="request-a-quota-increase-by-region-from-help--support"></a>도움말 + 지원에서 지역별 할당량 증가 요청

**도움말 + 지원에서**지역별 vCPU 할당량 증가를 요청하려면 다음을 수행하십시오.

1. Azure [포털](https://portal.azure.com) 메뉴에서 **도움말 + 지원을**선택합니다.

   !["도움말 + 지원" 링크](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **도움말 + 지원에서** **새 지원 요청을**선택합니다.

    ![새 지원 요청](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **구독의**경우 할당량을 늘리려는 구독을 선택합니다.

   ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 할당량 **유형의**경우 **다른 요청을**선택합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **다음:** **문제 세부 정보를**여는 해결 방법을 선택합니다. **설명에서**다음 정보를 제공합니다.

    1. **배포 모델의**경우 **리소스 관리자를**지정합니다.  
    1. **Region의**경우 필요한 지역(예: **미국 동부 2)을**지정합니다.  
    1. **새 제한의**경우 지역에 대한 새 vCPU 제한을 지정합니다. 이 값은 이 구독에 대한 개별 SKU 계열에 대해 승인된 할당량의 합계를 초과해서는 안 됩니다.

    ![할당량 요청에 대한 세부 정보 입력](./media/resource-manager-core-quotas-request/regional-details.png)

1. **검토 + 만들기를** 선택하여 지원 요청을 계속 만듭니다.

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>구독에서 지역별 할당량 증가 요청

구독에서 지역별 vCPU 할당량 증가를 요청하려면 다음을 **수행합니다.**

1. Azure [포털에서](https://portal.azure.com)구독을 검색하고 **선택합니다.**

   ![Azure 포털의 구독으로 이동](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘리려는 구독을 선택합니다.

   ![수정할 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 왼쪽 창에서 사용 **+ 할당량을 선택합니다.**

   ![사용 및 할당량 링크 따르기](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 상단에서 **증가 요청을**선택합니다.

   ![할당량을 늘리려면 선택](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형에서** **다른 요청을**선택합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. **다음:** **문제 세부 정보를**여는 해결 방법을 선택합니다. **설명** 상자에 다음과 같은 추가 정보를 제공합니다.

    1. **배포 모델의**경우 **리소스 관리자를**지정합니다.  
    1. **Region의**경우 필요한 지역(예: **미국 동부 2)을**지정합니다.  
    1. **새 제한의**경우 지역에 대한 새 vCPU 제한을 지정합니다. 이 값은 이 구독에 대한 개별 SKU 계열에 대해 승인된 할당량의 합계를 초과해서는 안 됩니다.

    ![세부 정보 입력](./media/resource-manager-core-quotas-request/regional-details.png)

1. **검토 + 만들기를** 선택하여 지원 요청을 계속 만듭니다.
