---
title: VM 당 Azure vCPU 할당량 증가 요청 | Microsoft Docs
description: VM 당 vCPU 할당량 증가 요청 수
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 280561126186e4c70399b3a1ddd177ff4eb54400
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850043"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>표준 할당량: VM 시리즈 당 vCPU 제한 증가

리소스 관리자는 가상 컴퓨터에 대 한 두 가지 유형의 vCPU 할당량을 지원 합니다. **종 량 제 vm 및 예약 VM 인스턴스는** 표준 할당량을 사용 합니다. **지점 vm** 은 스폿 할당량을 사용 합니다. 종 량 제 및 예약 VM 인스턴스에 대 한 표준 vCPU 할당량은 각 지역의 각 구독에 대해 두 개의 계층으로 적용 됩니다.

첫 번째 계층은 **총 지역 vCPUs 한도** (모든 vm 시리즈에서)이 고 두 번째 계층은 **Vm 당 시리즈 vcpus 제한** (예: Dv3 시리즈 vcpus)입니다. 새 VM이 배포 될 때마다 해당 VM 시리즈에 대 한 새로운 및 기존 vCPUs 사용량의 합계는 특정 VM 시리즈에 대해 승인 된 Vcpus 할당량을 초과 하지 않아야 합니다. 또한 모든 VM 시리즈에서 배포 된 새로운 총 및 기존 vCPU 수는 해당 구독에 대해 승인 된 총 지역 Vcpu 할당량을 초과할 수 없습니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다.
Azure Portal에서 VM 시리즈에 대 한 vCPUs 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량을 늘리면 전체 지역 vCPUs 한도가 같은 양만큼 자동으로 증가 합니다. 

[가상 머신 vCPU 할당량 페이지](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) 및 [Azure 구독 및 서비스 제한 페이지](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)에서 표준 vcpu 할당량에 대해 자세히 알아보세요. 

표준 [할당량에](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)대 한 지역 vcpu 제한을 높이는 방법에 대해 자세히 알아보세요. 

이에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)에서 **지점 VM vcpu 제한** 을 참조 하세요.

**도움말 + 지원** 블레이드 또는 포털의 **사용량 + 할당량** 블레이드를 통해 **VM 시리즈 당 표준 vcpu 할당량 한도** 를 늘릴 수 있습니다.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>도움말 + 지원 블레이드를 사용 하 여 구독 수준에서 VM 시리즈 당 표준 vCPU 할당량 증가를 요청 합니다.

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 도움말 + 지원 ' 블레이드를 통해 지원 요청을 만듭니다. 

단일 지원 사례를 통해 여러 지역에 대 한 할당량을 요청할 수도 있습니다. 자세한 내용은 아래의 11 단계를 참조 하세요.

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

7. **할당량 정보** 패널에서 **배포 모델** 을 선택 하 고 위치를 선택 **합니다.**

   ![할당량 정보 DM](./media/resource-manager-core-quotas-request/1-7.png)

8. 선택한 위치에 대해 **유형** 값을 **' 표준 '** 으로 선택 합니다. **유형** 필드에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 및 별색 할당량 유형을 모두 요청할 수 있습니다. 이 [페이지](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot) 의 **집중 된 할당량 한도** 를 늘리려면 자세히 알아보세요.

   ![SKU 제품군](./media/resource-manager-core-quotas-request/1-8.png)

9. 증가를 요구 하는 **SKU 패밀리** 를 선택 합니다.

   ![SKU 제품군](./media/resource-manager-core-quotas-request/1-9.png)

10. 구독에 대한 새 한도를 입력합니다. 줄을 제거 하려면 SKU 제품군 드롭다운에서 SKU를 선택 취소 하거나 삭제 "x" 아이콘을 클릭 합니다. 

   ![새 제한](./media/resource-manager-core-quotas-request/1-10.png)

11. 두 개 이상의 위치에 대 한 할당량을 요청 하려면 드롭다운에서 다른 **위치** 를 확인 하 고 적절 한 VM 유형을 선택할 수 있습니다. 이 단계에서는 새 위치에 대해 이전 **위치** 에 대해 선택한 SKU 제품군을 미리 로드 하 고 원하는 새 제한을 입력 하기만 하면 됩니다.

   ![여러 위치](./media/resource-manager-core-quotas-request/1-11.png)
   
12. 각 SKU 제품군에 대해 원하는 할당량을 입력 한 후 할당량 정보 패널에서 **저장 후 계속** 을 클릭 하 여 지원 요청 만들기를 계속 합니다.

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>사용량 + 할당량 블레이드를 사용 하 여 구독 수준에서 VM 시리즈 당 표준 vCPU 할당량 증가 요청

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 사용 + 할당량 ' 블레이드를 통해 지원 요청을 만듭니다.

단일 지원 사례를 통해 **여러 지역에 대 한 할당량을 요청할** 수도 있습니다. 자세한 내용은 아래의 10 단계를 참조 하세요.

1. https://portal.azure.com 에서 **구독**을 선택합니다.

   ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

   ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

   ![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

   ![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5. **계산-VM (코어-vCPUs) 구독 제한** 을 선택 합니다. 

   ![양식 작성](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. **할당량 정보** 패널에서 배포 모델을 선택 하 고 위치를 선택 합니다.

   ![할당량 문제 블레이드](./media/resource-manager-core-quotas-request/1-1-6.png)

7. 선택한 위치에 대해 **유형** 값을 **' 표준 '** 으로 선택 합니다. **유형** 필드에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 및 낮은 우선 순위 할당량 유형을 요청할 수 있습니다. 이 [페이지](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)의 **지점 vcpus 제한** 에 대해 자세히 알아보세요.

   ![SKU 시리즈 선택됨](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. 증가를 요구 하는 **SKU 패밀리** 를 선택 합니다.

   ![SKU 시리즈 선택됨](./media/resource-manager-core-quotas-request/1-1-8.png)

9. 구독에 대한 새 한도를 입력합니다. 줄을 제거 하려면 SKU 제품군 드롭다운에서 SKU를 선택 취소 하거나 삭제 "x" 아이콘을 클릭 합니다. 

   ![SKU 새 할당량 요청](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. 두 개 이상의 위치에 대 한 할당량을 요청 하려면 드롭다운에서 다른 **위치** 를 확인 하 고 적절 한 VM 유형을 선택할 수 있습니다. 이 단계에서는 새 위치에 대해 이전 **위치** 에 대해 선택한 SKU 제품군을 미리 로드 하 고 원하는 새 제한을 입력 하기만 하면 됩니다.
   
    ![SKU 새 할당량 요청](./media/resource-manager-core-quotas-request/1-1-10.png)
 
