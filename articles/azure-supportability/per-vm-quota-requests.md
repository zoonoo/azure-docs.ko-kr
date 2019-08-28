---
title: VM 당 Azure vCPU 할당량 증가 요청 | Microsoft Docs
description: VM 당 vCPU 할당량 증가 요청 수
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9caf82d2fa4683e845d23b5496f9e7b8f9682ec7
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234843"
---
# <a name="vm-series-vcpu-limit-increase"></a>VM 시리즈 vCPU 제한 증가

가상 머신 및 가상 머신 확장 집합에 대 한 리소스 관리자 vCPU 할당량은 각 지역의 각 구독에 대해 두 개의 계층으로 적용 됩니다. 

첫 번째 계층은 **총 지역 vCPUs 한도** (모든 vm 시리즈에서)이 고 두 번째 계층은 **Vm 시리즈 vcpus 제한** (예: D 시리즈 vcpus)입니다. 새 VM을 배포할 때마다 해당 VM 시리즈에 대 한 새로운 및 기존 vCPUs 사용량의 합계는 특정 VM 시리즈에 대해 승인 된 Vcpus 할당량을 초과 하지 않아야 합니다. 또한 모든 VM 시리즈에서 배포 된 새로운 총 및 기존 vCPU 수는 해당 구독에 대해 승인 된 총 지역 Vcpu 할당량을 초과할 수 없습니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다.
Azure Portal에서 VM 시리즈에 대 한 vCPUs 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량을 늘리면 전체 지역 vCPUs 한도가 같은 양만큼 자동으로 증가 합니다. 

[가상 머신 vCPU 할당량 페이지](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) 및 [Azure 구독 및 서비스 제한](https://aka.ms/quotalimits) 페이지에서 할당량에 대해 자세히 알아보세요. 

이제 포털에서 **도움말 + 지원** 블레이드 또는 **사용량 + 할당량** 블레이드를 통해 증가를 요청할 수 있습니다. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>VM 시리즈 당 요청- **도움말 + 지원** 블레이드를 사용 하 여 구독 수준에서 vcpu 할당량 증가

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 도움말 + 지원 ' 블레이드를 통해 지원 요청을 만듭니다. 

1. [https://portal.azure.com](https://portal.azure.com )을 선택 **도움말 + 지원**합니다.

   ![도움말 + 지원](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **새 지원 요청**을 선택합니다. 

     ![새 지원 요청](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 문제 유형 드롭다운에서 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.

   ![문제 유형 드롭다운](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 할당량을 늘려야 하는 구독을 선택합니다.

   ![구독 newSR 선택](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **할당량 유형** 드롭다운에서 **계산 VM (코어-vcpus) 구독 제한을 늘립니다** .를 선택 합니다. 

   ![할당량 유형 선택](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. **문제 세부**정보에서 **세부 정보 제공**을 클릭 하 여 요청을 처리 하는 데 도움이 되는 추가 정보를 제공 합니다.

   ![세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details.png)

7. **할당량 정보** 패널에서 배포 모델을 선택 하 고 위치를 선택 합니다.

   ![할당량 정보 DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. 증가 해야 하는 **SKU 제품군** 을 선택 합니다. 

   ![SKU 제품군](./media/resource-manager-core-quotas-request/sku-family.png)

9. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 SKU 제품군 드롭다운에서 SKU 선택을 해제하거나 삭제 "x" 아이콘을 클릭합니다. 각 SKU 제품군에 대해 원하는 할당량을 입력 한 후 할당량 정보 패널에서 **저장 후 계속** 을 클릭 하 여 지원 요청 만들기를 계속 합니다.

   ![새 제한](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>VM 시리즈 당 요청 **사용량 + 할당량** 블레이드를 사용 하 여 구독 수준에서 vcpu 할당량 증가

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 사용 + 할당량 ' 블레이드를 통해 지원 요청을 만듭니다. 

1. [https://portal.azure.com](https://portal.azure.com )에서 **구독**을 선택합니다.

   ![구독](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

   ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

   ![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

   ![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5. **계산-VM (코어-vCPUs) 구독 제한** 을 선택 합니다. 

   ![양식 작성](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. **할당량 정보** 패널에서 배포 모델을 선택 하 고 위치를 선택 합니다.

   ![할당량 문제 블레이드](./media/resource-manager-core-quotas-request/quota-details.png)

7. 증가 해야 하는 **SKU 제품군** 을 선택 합니다.

   ![SKU 시리즈 선택됨](./media/resource-manager-core-quotas-request/sku-family.png)

8. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 SKU 제품군 드롭다운에서 SKU 선택을 해제하거나 삭제 "x" 아이콘을 클릭합니다. 각 SKU 제품군에 대해 원하는 할당량을 입력 한 후 문제 단계 페이지에서 **저장을 클릭 하 고 계속** 을 클릭 하 여 지원 요청 만들기를 계속 합니다.

   ![SKU 새 할당량 요청](./media/resource-manager-core-quotas-request/new-limits.png)
 
