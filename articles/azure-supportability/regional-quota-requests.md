---
title: Azure 지역 할당량 증가 요청 | Microsoft Docs
description: 지역 할당량 증가 요청
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 132cf6ccfec5af9951f5dc6d6a3c6d3c81363d81
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850009"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>표준 할당량: 지역 vCPU 제한 증가 

리소스 관리자는 가상 컴퓨터에 대 한 두 가지 유형의 vCPU 할당량을 지원 합니다. **종 량 제 vm** 및 **예약 VM 인스턴스** 는 표준 할당량을 사용 합니다. **지점 vm** 은 스폿 할당량을 사용 합니다. 

종 량 제 및 예약 VM 인스턴스에 대 한 표준 vCPU 할당량은 각 지역의 각 구독에 대해 두 개의 계층으로 적용 됩니다.
 
첫 번째 계층은 **총 지역 vCPUs 한도** (모든 vm 시리즈에서)이 고 두 번째 계층은 **Vm 시리즈 vcpus 제한** (예: D 시리즈 vcpus)입니다. 새 VM이 배포 될 때마다 해당 VM 시리즈에 대 한 새로운 및 기존 vCPUs 사용량의 합계는 특정 VM 시리즈에 대해 승인 된 Vcpus 할당량을 초과 하지 않아야 합니다. 또한 모든 VM 시리즈에서 배포 된 새로운 총 및 기존 vCPU 수는 해당 구독에 대해 승인 된 총 지역 Vcpu 할당량을 초과할 수 없습니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다. Azure Portal에서 VM 시리즈에 대 한 vCPUs 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량을 늘리면 전체 지역 vCPUs 한도가 같은 양만큼 자동으로 증가 합니다.

새 구독을 만들 때 기본 총 지역 vCPUs가 모든 개별 VM 시리즈에 대 한 기본 Vcpus 할당량의 합계와 같지 않을 수 있습니다. 이로 인해 배포 하려는 각 개별 VM 시리즈에 대 한 할당량이 충분 하지만 모든 배포에 대 한 총 지역 vCPUs 할당량은 충분 하지 않은 구독을 만들 수 있습니다. 이 경우 전체 지역 vCPUs 제한을 명시적으로 늘리기 위해 요청을 제출 해야 합니다. 총 지역 vCPUs 제한은 해당 지역에 대 한 모든 VM 시리즈에서 승인 된 할당량의 합계를 초과할 수 없습니다.

[가상 머신 vCPU 할당량 페이지](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) 및 [Azure 구독 및 서비스 제한](https://aka.ms/quotalimits) 페이지에서 표준 vcpu 할당량에 대해 자세히 알아보세요.

이에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)에서 **지점 VM vcpu 제한** 을 참조 하세요.

**도움말 + 지원** 블레이드 또는 포털의 **사용량 + 할당량** 블레이드를 통해 **표준 VM의 총 지역 vcpu 한도** 를 늘릴 수 있습니다.

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>도움말 + 지원 블레이드를 사용 하 여 표준 할당량 요청 지역 vCPU 제한 구독 수준 증가

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 도움말 + 지원 ' 블레이드를 통해 지원 요청을 만듭니다. 

1. [https://portal.azure.com](https://portal.azure.com )을 선택 **도움말 + 지원**합니다.

![도움말 + 지원](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **새 지원 요청**을 선택합니다. 

![새 지원 요청](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 문제 유형 드롭다운에서 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.

![문제 유형 드롭다운](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 할당량을 늘려야 하는 구독을 선택합니다.

![구독 newSR 선택](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **할당량 유형** 드롭다운에서 **기타 요청** 을 선택 합니다.

![할당량 유형](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. **세부** 정보 창에서 아래 예제와 같이 추가 정보를 제공 하 여 요청을 처리 하 고 사례 만들기를 계속 합니다. 
    1.  **배포 모델** – ' 리소스 관리자 '를 지정 합니다.
    2.  **요청 된 지역** – 미국 동부 2와 같이 필요한 지역을 지정 합니다.
    3.  **새 제한 값** – 새 지역 제한을 지정 합니다. 이 구독에 대 한 개별 SKU 제품군에 대해 승인 된 할당량의 합계를 초과 해서는 안 됩니다.

![할당량 정보](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>**사용량 + 할당량** 블레이드를 사용 하 여 구독 수준에서 총 지역 vcpus 할당량 증가 요청

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 사용 + 할당량 ' 블레이드를 통해 지원 요청을 만듭니다. 

1. https://portal.azure.com 에서 **구독**을 선택합니다.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5. **할당량 유형** 드롭다운에서 **기타 요청** 을 선택 합니다.

![할당량 유형](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. **세부** 정보 창에서 아래 예제와 같이 추가 정보를 제공 하 여 요청을 처리 하 고 사례 만들기를 계속 합니다. 
    1.  **배포 모델** – ' 리소스 관리자 '를 지정 합니다.
    2.  **요청 된 지역** – 미국 동부 2와 같이 필요한 지역을 지정 합니다.
    3.  **새 제한 값** – 새 지역 제한을 지정 합니다. 이 구독에 대 한 개별 SKU 제품군에 대해 승인 된 할당량의 합계를 초과 해서는 안 됩니다.

![할당량 정보](./media/resource-manager-core-quotas-request/regional-details.png)



