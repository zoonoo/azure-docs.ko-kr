---
title: 스폿 할당량 | Microsoft Docs
description: 지점 할당량 요청
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850570"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>스폿 quota: 모든 VM 시리즈에 대해 증가 제한

지점 Vm은 다른 Azure 사용량 모델을 제공 하 여 Azure에서 종 량 제 또는 예약 된 VM 인스턴스 배포를 위해 필요에 따라 VM을 제거 하는 데 드는 비용을 절감 합니다. Vm에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)를 참조 하세요.

리소스 관리자는 가상 컴퓨터에 대 한 두 가지 유형의 vCPU 할당량을 지원 합니다. **종 량 제 vm 및 예약 VM 인스턴스는** 표준 할당량을 사용 합니다. **지점 vm** 은 스폿 할당량을 사용 합니다. 

**별색 할당량** 유형 리소스 관리자 vcpu 할당량은 단일 지역 제한으로 사용 가능한 모든 VM 시리즈에서 적용 됩니다.

새 지점 VM을 배포할 때마다 모든 스폿 VM 인스턴스에 대 한 새로운 및 기존 vCPUs 사용량의 합계가 승인 된 지점 Vcpus 할당량 제한을 초과 하면 안 됩니다. 스폿 할당량을 초과 하는 경우 스폿 VM 배포는 허용 되지 않습니다. Azure Portal에서 지점 vCPUs 할당량 한도 증가를 요청할 수 있습니다. 

가상 머신 vCPU 할당량 페이지 및 Azure 구독 및 서비스 제한 페이지에서 표준 vCPU 할당량에 대해 자세히 알아보세요. 이 [페이지](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)에서 표준 할당량에 대 한 지역 vcpu 제한을 높이는 방법에 대해 자세히 알아보세요.

이제 포털에서 **도움말 + 지원** 블레이드 또는 사용 **+ 할당량** 블레이드를 통해 **모든 VM 시리즈에 대 한 지점 할당량 제한을** 늘릴 수 있습니다.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>도움말 + 지원 블레이드를 사용 하 여 구독 당 모든 VM 시리즈에 대 한 요청 지점 할당량 한도 증가

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 도움말 + 지원 ' 블레이드를 통해 지원 요청을 만듭니다.

단일 지원 사례를 통해 **여러 지역에 대 한 할당량을 요청할** 수도 있습니다. 자세한 내용은 아래의 10 단계를 참조 하세요. 


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
   
7.  **할당량 정보*** 패널에서 **배포 모델** 을 선택 하 고 **위치**를 선택 합니다.

![세부 정보 제공](./media/resource-manager-core-quotas-request/3-7.png)

8. 선택한 위치에 대해 **' 스폿 '** **형식** 으로 값을 선택 합니다. **유형** 필드에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 및 별색 할당량 유형을 모두 요청할 수 있습니다. 이 [페이지](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)에서 **VM 시리즈 당 표준 할당량을 높이** 는 방법에 대해 자세히 알아보세요.

![세부 정보 제공](./media/resource-manager-core-quotas-request/3-8.png)

9.  구독에 대해 원하는 새 제한을 입력 합니다. 
 
 ![세부 정보 제공](./media/resource-manager-core-quotas-request/3-9.png)

10. 두 개 이상의 위치에 대 한 할당량을 요청 하려면 드롭다운에서 다른 위치를 확인 하 고 적절 한 VM 유형을 선택할 수 있습니다. 그런 다음 원하는 새 제한을 입력할 수 있습니다.

![세부 정보 제공](./media/resource-manager-core-quotas-request/3-10.png)

11. 원하는 할당량을 입력 한 후 할당량 정보 패널에서 **저장 후 계속** 을 클릭 하 여 지원 요청 만들기를 계속 합니다.

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>사용량 + 할당량 블레이드를 사용 하 여 구독 당 모든 VM 시리즈에 대 한 요청 지점 할당량 한도 증가

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 사용 + 할당량 ' 블레이드를 통해 지원 요청을 만듭니다.

단일 지원 사례를 통해 **여러 지역에 대 한 할당량을 요청할** 수도 있습니다. 자세한 내용은 아래의 9 단계를 참조 하세요. 

1.  https://portal.azure.com 에서 **구독**을 선택합니다.

 ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  할당량을 늘려야 하는 구독을 선택합니다.

 ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  **사용량 + 할당량**을 선택합니다.

 ![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  오른쪽 위의 모서리에서 **요청 증가를 선택 합니다.**

   ![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5.  **계산-VM (코어-vCPUs) 구독 제한** 을 선택 합니다.

  ![양식 작성](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  **할당량 정보** 패널에서 배포 모델을 선택 하 고 위치를 선택 합니다.

  ![양식 작성](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  선택한 위치에 대해 **' 스폿 '** **형식** 으로 값을 선택 합니다. **유형** 필드에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 및 별색 할당량 유형을 모두 요청할 수 있습니다. 이 [페이지](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)에서 **VM 시리즈 당 표준 할당량을 높이** 는 방법에 대해 자세히 알아보세요.

  ![양식 작성](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  구독에 대해 원하는 새 제한을 입력 합니다.

  ![양식 작성](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  두 개 이상의 위치에 대 한 할당량을 요청 하려면 드롭다운에서 다른 **위치** 를 확인 하 고 적절 한 VM 유형을 선택할 수 있습니다. 그런 다음 원하는 새 제한을 입력할 수 있습니다.

  ![양식 작성](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. 원하는 할당량을 입력 한 후 할당량 정보 패널에서 **저장 후 계속** 을 클릭 하 여 지원 요청 만들기를 계속 합니다.


