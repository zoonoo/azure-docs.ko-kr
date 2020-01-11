---
title: Azure VM 시리즈 당 vCPU 할당량 한도 증가 요청 | Microsoft Docs
description: 이 문서에서는 VM vCPU 당 할당량 한도를 요청 하는 방법을 설명 합니다.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: fc59ffda245834c716067bc63e6c3745fa5d23b3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897248"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>표준 할당량: VM 시리즈로 한도 증가

Azure Resource Manager는 가상 컴퓨터에 대 한 두 가지 유형의 vCPU 할당량을 지원 합니다.
* *종 량 제 vm* 및 *예약 vm 인스턴스* 는 *표준 vcpu 할당량*을 따릅니다.
* *지점 vm* 에는 *별색 vcpu 할당량이*적용 됩니다. 

종 량 제 및 예약 VM 인스턴스에 대 한 표준 vCPU 할당량은 각 지역의 각 구독에 대해 두 개의 계층으로 적용 됩니다.
* 첫 번째 계층은 모든 VM 시리즈에서 *총 지역 vCPUs 한도* 입니다.
* 두 번째 계층은 *VM 별 시리즈 vcpus 제한* (예: Dv3 시리즈 vcpus)입니다. 

새 스폿 VM을 배포할 때마다 해당 VM 시리즈에 대 한 새로운 새 및 기존 vCPU 사용량은 특정 VM 시리즈에 대해 승인 된 vCPU 할당량을 초과 하지 않아야 합니다. 또한 모든 VM 시리즈에 배포 되는 새로운 및 기존 vCPUs의 총 수는 구독에 대해 승인 된 총 지역 Vcpus 할당량을 초과할 수 없습니다. 이러한 할당량 중 하나가 초과 되 면 VM 배포는 허용 되지 않습니다.

Azure Portal를 사용 하 여 VM 시리즈에 대 한 vCPU 할당량 한도 증가를 요청할 수 있습니다. VM 시리즈 할당량을 늘리면 전체 지역 vCPU 한도가 같은 양만큼 자동으로 증가 합니다. 

표준 vCPU 할당량에 대 한 자세한 내용은 [가상 머신 vcpu 할당량](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) 및 [Azure 구독 및 서비스 제한](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)을 참조 하세요. 

표준 할당량에 대 한 지역별 vCPU 제한을 늘리는 방법에 대 한 자세한 내용은 [표준 할당량: 지역별 제한 증가](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)를 참조 하세요. 

지점 VM vCPU 한도를 늘리는 방법에 대 한 자세한 내용은 [스폿 quota: 모든 VM 시리즈에 대 한 제한 증가](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)를 참조 하세요.

다음 섹션에 설명 된 대로 두 가지 방법 중 하나로 VM 시리즈 당 표준 vCPU 할당량 한도를 늘릴 수 있습니다.

## <a name="request-a-standard-quota-increase-from-the-help--support-pane"></a>"도움말 + 지원" 창에서 표준 할당량 증가를 요청 합니다.

**도움말 + 지원** 창에서 VM 시리즈 당 표준 vcpu 할당량 증가를 요청 하려면 다음을 수행 합니다. 

> [!NOTE]
> 단일 지원 사례를 통해 여러 지역의 할당량 한도 증가를 요청할 수도 있습니다. 자세한 내용은 8 단계를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)왼쪽 창에서 **도움말 + 지원**을 선택 합니다.

   !["도움말 + 지원" 링크](./media/resource-manager-core-quotas-request/helpsupport.png)
 
1. **도움말 + 지원** 창에서 **새 지원 요청**을 선택 합니다. 

    ![새 지원 요청](./media/resource-manager-core-quotas-request/newsupportrequest.png)

1. **문제 유형** 드롭다운 목록에서 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.

   !["문제 유형" 드롭다운 목록](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

1. **구독** 드롭다운 목록에서 할당량을 늘릴 구독을 선택 합니다.

   !["구독" 드롭다운 목록](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
1. **할당량 유형** 드롭다운 목록에서 **계산-VM (코어-vcpus) 구독 한도 늘리기**를 선택 합니다. 

   !["할당량 유형" 드롭다운 목록](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. **세부** 정보 탭의 **문제 세부**정보에서 세부 정보 **제공**을 선택 하 고 요청을 처리 하는 데 도움이 되는 추가 정보를 입력 합니다.

   !["세부 정보 제공" 링크](./media/resource-manager-core-quotas-request/provide-details.png)

1. 오른쪽 위에 있는 **할당량 정보** 창에서 다음을 수행 합니다.

   !["할당량 정보" 창](./media/resource-manager-core-quotas-request/1-7.png)

   a. **배포 모델** 드롭다운 목록에서 적절 한 모델을 선택 합니다.

   b. **위치 드롭다운 목록** 에서 위치를 선택 합니다. 선택한 위치에 대해 **유형 선택** 상자에 **표준** **을 입력**합니다.

   !["할당량 세부 정보" 창-할당량 유형](./media/resource-manager-core-quotas-request/1-8.png)

   **유형**에서 다중 선택 지원을 통해 단일 지원 사례에서 표준 및 별색 할당량 유형을 모두 요청할 수 있습니다.
   
   집중 할당량 한도에 대 한 자세한 내용은 [가상 머신 확장 집합에 대 한 Azure 스폿 vm](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)을 참조 하세요.

   다. **표준** 드롭다운 목록 아래에서 할당량을 증가 시킬 SKU 시리즈를 선택 합니다.

   !["할당량 세부 정보" 창-SKU 시리즈](./media/resource-manager-core-quotas-request/1-9.png)

   d. 이 구독에 대해 원하는 새 할당량 한도를 입력 합니다. 목록에서 SKU를 제거 하려면 SKU 옆의 확인란 선택을 취소 하거나 **삭제** (X) 아이콘을 선택 합니다. 

   !["새 vCPU 제한" 텍스트 상자](./media/resource-manager-core-quotas-request/1-10.png)

1. 두 개 이상의 위치에 대해 할당량 증가를 요청 하려면 드롭다운 목록에서 추가 위치를 선택한 다음 적절 한 VM 유형을 선택 합니다. 그런 다음 추가 위치에 적용 되는 한도를 입력할 수 있습니다.

   !["할당량 정보" 창의 추가 위치](./media/resource-manager-core-quotas-request/1-11.png)
   
1. **저장을 선택 하 고 계속** 을 선택 하 여 지원 요청을 계속 만듭니다.

## <a name="request-a-standard-quota-increase-from-the-subscriptions-pane"></a>"구독" 창에서 표준 할당량 증가를 요청 합니다.

**구독** 창에서 VM 시리즈 당 표준 vcpu 할당량 증가를 요청 하려면 다음을 수행 합니다.

> [!NOTE]
> 단일 지원 사례를 통해 여러 지역의 할당량 한도 증가를 요청할 수도 있습니다. 자세한 내용은 7 단계를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **구독**을 선택 합니다.

   !["구독" 링크](./media/resource-manager-core-quotas-request/subscriptions.png)

1. 할당량을 늘릴 구독을 선택 합니다.

   !["구독" 창](./media/resource-manager-core-quotas-request/select-subscription.png)

1. **\<구독 이름 >** 페이지의 왼쪽 창에서 **사용량 + 할당량**을 선택 합니다.

   !["사용량 + 할당량" 링크](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

1. 오른쪽 상단에서 **요청 증가**를 선택 합니다.

   ![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

1. **할당량 유형** 드롭다운 목록에서 **계산-VM (코어-vcpus) 구독 한도 늘리기**를 선택 합니다.

   !["할당량 유형" 드롭다운 목록](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
1. 오른쪽 위에 있는 **할당량 정보** 창에서 다음을 수행 합니다.

   !["할당량 정보" 창](./media/resource-manager-core-quotas-request/1-1-6.png)

   a. **배포 모델** 드롭다운 목록에서 적절 한 모델을 선택 합니다.

   b. **위치 드롭다운 목록** 에서 위치를 선택 합니다. 
   
   다. 선택한 위치의 **형식**에서 **유형 선택**을 선택한 다음 **표준** 확인란을 선택 합니다.

   !["표준" 확인란](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   **유형**아래에서 다중 선택 지원을 통해 단일 지원 사례의 표준 및 낮은 우선 순위 할당량 유형을 모두 요청할 수 있습니다.
   
   집중 할당량 한도에 대 한 자세한 내용은 [가상 머신 확장 집합에 대 한 Azure 스폿 vm](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)을 참조 하세요.

   d. **표준** 드롭다운 목록 아래에서 할당량을 증가 시킬 SKU 시리즈를 선택 합니다.

   !["할당량 세부 정보" 창-SKU 시리즈](./media/resource-manager-core-quotas-request/1-1-8.png)

   e. 이 구독에 대해 원하는 새 할당량 한도를 입력 합니다. 목록에서 SKU를 제거 하려면 SKU 옆의 확인란 선택을 취소 하거나 **삭제** (X)를 선택 합니다. 

   !["새 vCPU 제한" 텍스트 상자](./media/resource-manager-core-quotas-request/1-1-9.png)
   
1. 두 개 이상의 위치에 대해 할당량 증가를 요청 하려면 드롭다운 목록에서 추가 위치를 선택한 다음 적절 한 VM 유형을 선택 합니다. 

   이 단계에서는 이전 위치에 대해 선택한 SKU 시리즈를 미리 로드 합니다. 추가 시리즈에 적용 하려는 할당량 한도를 입력 합니다.
   
   !["할당량 정보" 창의 추가 위치](./media/resource-manager-core-quotas-request/1-1-10.png)
 
1. **저장을 선택 하 고 계속** 을 선택 하 여 지원 요청을 계속 만듭니다.
