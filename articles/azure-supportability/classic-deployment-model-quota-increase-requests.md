---
title: Azure 클래식 배포 모델 | Microsoft Docs
description: Azure 클래식 배포 모델
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 70ad44eade871d52591014ee24e645b95c52f1e5
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234854"
---
# <a name="classic-deployment-model"></a>클래식 배포 모델

클래식 배포 모델은 가상 머신 및 가상 머신 확장 집합에 대 한 글로벌 vCPU 할당량 한도를 적용 하는 이전 세대의 Azure 배포 모드입니다. 클래식 배포 모델은 더 이상 권장 되지 않으며 이제 리소스 관리자 모델에 의해 대체 되었습니다. 이러한 두 배포 모델 및의 이점에 대 한 자세한 내용은 리소스 관리자 배포 모델 페이지를 참조 리소스 관리자. 새 구독을 만들 때 vCPUs의 기본 할당량이 할당 됩니다. 클래식 배포 모델을 사용 하 여 새 VM을 배포 하는 경우 언제 든 지 모든 지역에서 신규 및 기존 vCPUs 사용량의 합계는 클래식 배포 모델에 대해 승인 된 Vcpus 할당량을 초과 하지 않아야 합니다. [Azure 구독 및 서비스 제한 페이지](https://aka.ms/quotalimits) 의 할당량에 대 한 자세한 정보

도움말 + 지원 블레이드 또는 포털의 사용량 + 할당량 블레이드를 통해 클래식 배포 모델에 대 한 vCPUs 제한의 증가를 요청할 수 있습니다.

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

7. **할당량 정보** 패널에서 클래식을 선택 하 고 위치를 선택 합니다.

   ![할당량 정보 DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. 증가 해야 하는 **SKU 제품군** 을 선택 합니다. 

   ![SKU 제품군](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 SKU 제품군 드롭다운에서 SKU 선택을 해제하거나 삭제 "x" 아이콘을 클릭합니다. 각 SKU 제품군에 대해 원하는 할당량을 입력 한 후 할당량 정보 패널에서 **저장 후 계속** 을 클릭 하 여 지원 요청 만들기를 계속 합니다.

   ![새 제한](./media/resource-manager-core-quotas-request/new-limits-classic.png)

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
   
6. **문제 세부**정보에서 **세부 정보 제공**을 클릭 하 여 요청을 처리 하는 데 도움이 되는 추가 정보를 제공 합니다.

   ![세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details.png)

7. **할당량 정보** 패널에서 클래식을 선택 하 고 위치를 선택 합니다.

   ![할당량 정보 DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. 증가 해야 하는 **SKU 제품군** 을 선택 합니다. 

   ![SKU 제품군](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 SKU 제품군 드롭다운에서 SKU 선택을 해제하거나 삭제 "x" 아이콘을 클릭합니다. 각 SKU 제품군에 대해 원하는 할당량을 입력 한 후 할당량 정보 패널에서 **저장 후 계속** 을 클릭 하 여 지원 요청 만들기를 계속 합니다.

   ![새 제한](./media/resource-manager-core-quotas-request/new-limits-classic.png)

