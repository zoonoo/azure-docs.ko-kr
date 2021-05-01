---
title: Azure 클래식 배포 모델
description: 이제 Resource Manager 모델로 교체된 클래식 배포 모델은 VM 및 가상 머신 확장 집합에 대해 전역 vCPU 할당량 제한을 적용합니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745607"
---
# <a name="classic-deployment-model"></a>클래식 배포 모델

클래식 배포 모델은 이전 세대 Azure 배포 모델입니다. 가상 머신 및 가상 머신 확장 집합에 대한 전역 vCPU 할당량 제한을 적용합니다. 클래식 배포 모델은 더 이상 권장되지 않으며 이제 Resource Manager 모델로 대체되었습니다.

이러한 두 배포 모델에 대한 자세한 내용 및 Resource Manager 사용의 이점에 대한 자세한 내용은 [Resource Manager 및 클래식 배포](../../azure-resource-manager/management/deployment-models.md)를 참조하세요.

새 구독을 만들 때 vCPU의 기본 할당량이 할당됩니다. 클래식 배포 모델을 사용하여 새 가상 머신을 배포할 때마다 모든 지역에서 신규 및 기존 vCPU 사용량의 합계는 클래식 배포 모델에 대해 승인된 vCPU 할당량을 초과하지 않아야 합니다.

할당량에 대해 자세히 알아보려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.

클래식 배포 모델에 대한 vCPU 할당량 한도를 늘리도록 요청할 수 있습니다. Azure Portal에서 **도움말 + 지원** 또는 **사용량 + 할당량** 을 사용합니다.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>도움말 + 지원을 사용하여 구독 수준에서 VM 시리즈 vCPU 할당량에 대한 요청 증가

Azure Portal에서 **도움말 + 지원** 을 사용하여 지원 요청을 만들려면 아래 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원** 을 선택합니다.

   ![Azure Portal에서 도움말 + 지원 선택](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **새 지원 요청** 을 선택합니다.

   ![Azure Portal에서 새로운 지원 요청 만들기](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제 유형** 에서 **서비스 및 구독 제한(할당량)** 을 선택합니다.

   ![문제 유형으로 할당량 선택](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 할당량을 늘릴 구독을 선택합니다.

   ![할당량을 늘릴 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형** 에서 **컴퓨팅 - VM(코어-vCPU) 구독 제한을 늘립니다.** 를 선택합니다.

   ![늘릴 할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음: 솔루션** 을 선택하여 **문제 세부 정보** 를 엽니다. **세부 정보 제공** 을 선택하여 추가 정보를 제공합니다.

   ![요청에 도움을 주는 세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 세부 정보** 에서 **클래식** 을 선택하고 **위치** 를 선택합니다.

   ![배포 모델 및 위치를 포함하는 세부 정보 추가](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. **SKU 제품군** 에 대해 늘릴 SKU 제품군을 하나 이상 선택합니다.

   ![늘릴 SKU 제품군 지정](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 **SKU 제품군** 에서 SKU를 선택 취소하거나 삭제 "x" 아이콘을 클릭합니다. 각 SKU 제품군에 대한 할당량을 입력한 후에는 **할당량 세부 정보** 에서 **저장 및 계속** 을 선택하여 지원 요청을 계속합니다.

   ![새 제한 요청](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>사용량 + 할당량을 사용하여 구독 수준에서 VM 시리즈 vCPU 할당량에 대한 요청 증가

Azure Portal에서 **사용량 + 할당량** 을 사용하여 지원 요청을 만들려면 아래 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com)에서 **구독** 을 검색하고 선택합니다.

   ![Azure Portal에서 구독으로 이동](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘릴 구독을 선택합니다.

   ![수정할 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. **사용량 + 할당량** 을 선택합니다.

   ![구독에 대한 사용량 및 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 위 모서리에서 **증가 요청** 을 선택합니다.

   ![할당량을 늘리려면 선택](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형** 으로 **컴퓨팅-VM(코어-vCPU) 구독 제한을 늘립니다.** 를 선택합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음: 솔루션** 을 선택하여 **문제 세부 정보** 를 엽니다. **세부 정보 제공** 을 선택하여 추가 정보를 제공합니다.

   ![요청에 대한 세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 세부 정보** 에서 **클래식** 및 **위치** 를 선택합니다.

   ![배포 모델 및 위치를 포함하는 할당량 세부 정보 선택](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 증가를 위해 하나 이상의 SKU 제품군을 선택합니다.

   ![증가에 대한 SKU 패밀리 선택](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 **SKU 제품군** 에서 SKU를 선택 취소하거나 삭제 "x" 아이콘을 클릭합니다. 각 SKU 제품군에 대한 할당량을 입력한 후에는 **할당량 세부 정보** 에서 **저장 및 계속** 을 선택하여 지원 요청을 계속합니다.

   ![새 할당량 입력](./media/resource-manager-core-quotas-request/new-limits-classic.png)

