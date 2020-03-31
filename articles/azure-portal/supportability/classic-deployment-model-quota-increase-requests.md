---
title: Azure 클래식 배포 모델
description: 이제 리소스 관리자 모델로 대체된 클래식 배포 모델은 VM 및 가상 시스템 규모 집합에 대한 전역 vCPU 할당량 제한을 적용합니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835636"
---
# <a name="classic-deployment-model"></a>클래식 배포 모델

클래식 배포 모델은 이전 세대 Azure 배포 모델입니다. 가상 시스템 및 가상 시스템 크기 집합에 대한 전역 vCPU 할당량 제한을 적용합니다. 클래식 배포 모델은 더 이상 권장되지 않으며 이제 리소스 관리자 모델로 대체됩니다.

이러한 두 배포 모델과 리소스 관리자 사용의 이점에 대해 자세히 알아보려면 [리소스 관리자 및 기존 배포를](../../azure-resource-manager/management/deployment-models.md)참조하십시오.

새 구독이 만들어지면 vCPU의 기본 할당량이 할당됩니다. 클래식 배포 모델을 사용하여 새 가상 컴퓨터를 배포할 때마다 모든 리전에서 새 가상 및 기존 vCPU 사용량의 합계가 클래식 배포 모델에 대해 승인된 vCPU 할당량을 초과해서는 안 됩니다.

할당량에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건을](../../azure-resource-manager/management/azure-subscription-service-limits.md)참조하십시오.

클래식 배포 모델에 대한 vCPU 할당량 제한 증가를 요청할 수 있습니다. Azure 포털에서 **도움말 + 지원** 또는 사용량 + **할당량을** 사용합니다.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>도움말 + 지원을 사용하여 구독 수준에서 VM 시리즈별 vCPU 할당량 증가

아래 지침에 따라 Azure 포털에서 **도움말 + 지원을** 사용하여 지원 요청을 만듭니다.

1. Azure [포털](https://portal.azure.com) 메뉴에서 **도움말 + 지원을**선택합니다.

   ![Azure 포털에서 도움말 + 지원 선택](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **새 지원 요청**을 선택합니다.

   ![Azure 포털에서 새 지원 요청 만들기](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제 유형에서** **서비스 및 구독 한도(할당량)를 선택합니다.**

   ![문제 유형으로 할당량 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 할당량을 늘리려는 구독을 선택합니다.

   ![할당량을 늘릴 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형의**경우 **계산 -VM(코어-vCPU) 구독 한도 증가를 선택합니다.**

   ![증가할 할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음:** **문제 세부 정보를**여는 해결 방법을 선택합니다. 추가 정보를 제공하려면 **세부 정보 제공을** 선택합니다.

   ![요청에 도움이 되는 세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 세부 정보에서** **클래식을** 선택하고 **위치를**선택합니다.

   ![배포 모델 및 위치를 포함한 세부 정보 추가](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. **SKU 제품군의**경우 하나 이상의 SKU 패밀리를 선택하여 늘릴 수 있습니다.

   ![증가할 SKU 패밀리 지정](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 구독에 대한 새 한도를 입력합니다. 선을 제거하려면 **SKU 패밀리에서** SKU를 선택 취소하거나 "X" 삭제 아이콘을 선택합니다. 각 SKU 제품군에 대한 할당량을 입력한 후 **할당량 세부 정보** 저장 및 **계속을** 선택하여 지원 요청을 계속합니다.

   ![새 제한 요청](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>사용량 + 할당량을 사용하여 구독 수준에서 VM 시리즈별 vCPU 할당량 증가

아래 지침에 따라 Azure 포털에서 **사용량 + 할당량을** 사용하여 지원 요청을 만듭니다.

1. Azure [포털에서](https://portal.azure.com)구독을 검색하고 **선택합니다.**

   ![Azure 포털의 구독으로 이동](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘리려는 구독을 선택합니다.

   ![수정할 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. **사용 + 할당량을 선택합니다.**

   ![구독에 대한 사용량 및 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 상단 모서리에서 **증가 요청을**선택합니다.

   ![할당량을 늘리려면 선택](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형에**따라 **컴퓨트 VM(코어-vCPU) 구독 제한이 증가합니다.**

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음:** **문제 세부 정보를**여는 해결 방법을 선택합니다. 추가 정보를 제공하려면 **세부 정보 제공을** 선택합니다.

   ![요청에 대한 세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 세부 정보에서** **클래식** 및 **위치**를 선택합니다.

   ![배포 모델 및 위치를 포함한 할당량 세부 정보 선택](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 증가를 위해 하나 이상의 SKU 패밀리를 선택합니다.

   ![증가를 위해 SKU 제품군선택](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 구독에 대한 새 한도를 입력합니다. 선을 제거하려면 **SKU 패밀리에서** SKU를 선택 취소하거나 "X" 삭제 아이콘을 선택합니다. 각 SKU 제품군에 대한 할당량을 입력한 후 **할당량 세부 정보** 저장 및 **계속을** 선택하여 지원 요청을 계속합니다.

   ![새 할당량 입력](./media/resource-manager-core-quotas-request/new-limits-classic.png)

