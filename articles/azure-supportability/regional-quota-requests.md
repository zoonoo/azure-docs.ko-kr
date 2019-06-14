---
title: Azure 지역 할당량 증가 요청 | Microsoft Docs
description: 지역 할당량 증가 요청
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 4c5524cf450959db7055ca5d032c81f79ebac077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083081"
---
# <a name="total-regional-vcpu-limit-increase"></a>지역별 총 vCPU 제한 증가 

통해 증가 요청할 수 있습니다 **도움말 + 지원** 블레이드 또는 **사용량 + 할당량** 포털에서 블레이드입니다. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>구독 수준 사용 하 여 지역별 총 Vcpu 할당량 증가 요청 합니다 **도움말 + 지원** 블레이드

Azure portal에서 제공 하는 Azure의 '도움말 + 지원' 블레이드를 통해 지원 요청을 만들려면 아래 지침을 따릅니다. 

1. https://portal.azure.com 을 선택 **도움말 + 지원**합니다.

![도움말 + 지원](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **새 지원 요청**을 선택합니다. 

![새 지원 요청](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 문제 유형 드롭다운 목록에서 선택 **서비스 및 구독 제한 (할당량)** 합니다.

![문제 유형 드롭다운](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 할당량을 늘려야 하는 구독을 선택합니다.

![구독 선택 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 선택 **다른 요청** 에 **할당량 유형** 드롭다운 합니다.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. **세부 정보** 창 요청 프로세스를 위해 아래 예제에 따라 추가 정보를 제공 하 고 사례 만들기를 계속 진행 합니다. 
    1.  **배포 모델** – ' 리소스 관리자 '를 지정 합니다.
    2.  **요청 된 지역** – 필요한 지역을 예: 미국 동부 2 지정 합니다.
    3.  **새로운 제한 값** – 새 지역 제한을 지정 합니다. 이이 구독에 대 한 개별 SKU 제품군에 대 한 승인 된 할당량의 합계를 넘지 않아야

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>구독 수준 사용 하 여 지역별 총 Vcpu 할당량 증가 요청 합니다 **사용량 + 할당량** 블레이드

Azure의 '사용량 + 할당량'를 통해 지원 요청을 만들려면 사용 하 여 아래 지침에 따라 블레이드에서 Azure portal에서 사용할 수 있습니다. 

1. https://portal.azure.com 에서 **구독**을 선택합니다.

![구독](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5. 선택 **다른 요청** 에 **할당량 유형** 드롭다운 합니다.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. **세부 정보** 창 요청 프로세스를 위해 아래 예제에 따라 추가 정보를 제공 하 고 사례 만들기를 계속 진행 합니다. 
    1.  **배포 모델** – ' 리소스 관리자 '를 지정 합니다.
    2.  **요청 된 지역** – 필요한 지역을 예: 미국 동부 2 지정 합니다.
    3.  **새로운 제한 값** – 새 지역 제한을 지정 합니다. 이이 구독에 대 한 개별 SKU 제품군에 대 한 승인 된 할당량의 합계를 넘지 않아야

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



