---
title: Azure 클래식 배포 모델
description: 이제 리소스 관리자 모델로 교체 되는 클래식 배포 모델은 Vm 및 가상 머신 확장 집합에 대해 전역 vCPU 할당량 제한을 적용 합니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: afdb8b2b677321239f240e74d49711195ac186c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763910"
---
# <a name="classic-deployment-model"></a>클래식 배포 모델

클래식 배포 모델은 이전 세대 Azure 배포 모델입니다. 가상 머신 및 가상 머신 확장 집합에 대 한 글로벌 vCPU 할당량 제한을 적용 합니다. 클래식 배포 모델은 더 이상 권장 되지 않으며 이제 리소스 관리자 모델에 의해 대체 됩니다.

이러한 두 배포 모델에 대 한 자세한 내용 및 리소스 관리자 사용의 이점에 대 한 자세한 내용은 [리소스 관리자 및 클래식 배포](../../azure-resource-manager/management/deployment-models.md)를 참조 하세요.

새 구독을 만들 때 vCPUs의 기본 할당량이 할당 됩니다. 클래식 배포 모델을 사용 하 여 새 가상 컴퓨터를 배포할 때마다 모든 지역에서 신규 및 기존 vCPU 사용량의 합계는 클래식 배포 모델에 대해 승인 된 vCPU 할당량을 초과 하지 않아야 합니다.

할당량에 대해 자세히 알아보려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.

클래식 배포 모델에 대 한 vCPU 할당량 한도를 늘릴 수 있습니다. Azure Portal에서 **도움말 + 지원** 또는 **사용량 + 할당량** 을 사용 합니다.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>VM에 대 한 요청 시리즈 시리즈-도움말 + 지원을 사용 하 여 구독 수준에서 vCPU 할당량 증가

Azure Portal에서 **도움말 + 지원을** 사용 하 여 지원 요청을 만들려면 아래 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택합니다.

   ![Azure Portal에서 도움말 + 지원을 선택 합니다.](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. **새 지원 요청**을 선택합니다.

   ![Azure Portal에서 새 지원 요청 만들기](./media/resource-manager-core-quotas-request/new-support-request.png)

1. **문제 유형**에서 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.

   ![문제 유형으로 할당량을 선택 합니다.](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 할당량을 늘릴 구독을 선택 합니다.

   ![할당량을 늘릴 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. **할당량 유형**에서 **계산-VM (코어-vcpus) 구독 제한을 늘립니다**.

   ![늘릴 할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음: 솔루션** 을 선택 하 여 **문제 세부 정보**를 엽니다. **세부 정보 제공** 을 선택 하 여 추가 정보를 제공 합니다.

   ![요청에 도움을 주는 세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 정보**에서 **클래식** 을 선택 하 고 **위치**를 선택 합니다.

   ![배포 모델 및 위치를 포함 하는 세부 정보 추가](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. **Sku 제품군**에 대해 늘리려면 sku 제품군을 하나 이상 선택 합니다.

   ![늘릴 SKU 제품군 지정](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 구독에 대한 새 한도를 입력합니다. 줄을 제거 하려면 **sku 제품군** 에서 sku를 선택 취소 하거나 삭제 "X" 아이콘을 선택 합니다. 각 SKU 제품군에 대 한 할당량을 입력 한 후에는 **할당량 세부 정보** 에서 **저장 및 계속** 을 선택 하 여 지원 요청을 계속 합니다.

   ![새 제한 요청](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>VM 시리즈 당 요청 사용량 + 할당량을 사용 하 여 구독 수준에서 vCPU 할당량 증가

Azure Portal에서 **사용량 + 할당량** 을 사용 하 여 지원 요청을 만들려면 아래 지침을 따르세요.

1. [Azure Portal](https://portal.azure.com)에서 **구독**을 검색 하 고 선택 합니다.

   ![Azure Portal의 구독으로 이동](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 할당량을 늘릴 구독을 선택 합니다.

   ![수정할 구독 선택](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. **사용량 + 할당량**을 선택 합니다.

   ![구독에 대 한 사용 및 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 오른쪽 위의 모서리에서 **요청 증가**를 선택 합니다.

   ![할당량을 늘리려면 선택 합니다.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. **할당량 유형**으로 **계산 VM (코어-vcpus) 구독 제한을 늘립니다** .를 선택 합니다.

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **다음: 솔루션** 을 선택 하 여 **문제 세부 정보**를 엽니다. **세부 정보 제공** 을 선택 하 여 추가 정보를 제공 합니다.

   ![요청에 대 한 세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. **할당량 정보**에서 **클래식** 및 **위치**를 선택 합니다.

   ![배포 모델 및 위치를 포함 하 여 할당량 세부 정보 선택](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 증가를 위해 하나 이상의 SKU 패밀리를 선택 합니다.

   ![증가에 대 한 SKU 패밀리를 선택 합니다.](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 구독에 대한 새 한도를 입력합니다. 줄을 제거 하려면 **sku 제품군** 에서 sku를 선택 취소 하거나 삭제 "X" 아이콘을 선택 합니다. 각 SKU 제품군에 대 한 할당량을 입력 한 후에는 **할당량 세부 정보** 에서 **저장 및 계속** 을 선택 하 여 지원 요청을 계속 합니다.

   ![새 할당량 입력](./media/resource-manager-core-quotas-request/new-limits-classic.png)

