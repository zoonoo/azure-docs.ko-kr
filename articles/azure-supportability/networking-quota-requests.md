---
title: 제한 증가 네트워킹 | Microsoft Docs
description: 네트워킹 제한 증가
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297268"
---
# <a name="networking-limit-increase"></a>네트워킹 제한 증가

현재 네트워킹 사용량 및 할당량을 보려면 방문할 수 있습니다 합니다 **사용량 + 할당량** 블레이드에서 Azure portal에서 합니다. 사용량을 사용할 수도 있습니다 [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)를 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) 또는 [네트워크 사용량 API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) 네트워크 사용량 및 한도를 보려면.

통해 증가 요청할 수 있습니다 **도움말 + 지원** 블레이드 또는 **사용량 + 할당량** 포털에서 블레이드입니다.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>구독 사용 하 여 수준의 네트워킹 할당량 증가 요청 합니다 **도움말 + 지원** 블레이드

Azure portal에서 제공 하는 Azure의 '도움말 + 지원' 블레이드를 통해 지원 요청을 만들려면 아래 지침을 따릅니다. 

1. https://portal.azure.com 을 선택 **도움말 + 지원**합니다.

    ![도움말 + 지원](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **새 지원 요청**을 선택합니다. 

    ![새 지원 요청](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 문제 유형 드롭다운 목록에서 선택 **서비스 및 구독 제한 (할당량)** 합니다.

    ![문제 유형 드롭다운](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 할당량을 늘려야 하는 구독을 선택합니다.

    ![구독 선택 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 선택 **네트워킹** 에 **할당량 유형** 드롭다운 합니다. 

    ![할당량 유형 선택](./media/networking-quota-request/select-quota-type-network.png)

6. **문제 세부 정보**를 클릭 하 여 보다 쉽게 처리할 수 있는 추가 정보를 요청 제공 **세부 정보를 제공**합니다.

    ![세부 정보를 제공 합니다.](./media/resource-manager-core-quotas-request/provide-details.png)

7. 에 **할당량 정보** 패널에서 배포 모델, 위치 및 리소스에 대 한 증가 요청 하려면를 선택 합니다.

    ![할당량 정보 DM](./media/networking-quota-request/quota-details-network.png)

8.  구독에 대한 새 한도를 입력합니다. 줄을 제거 하려면 리소스 드롭다운 목록에서 리소스를 선택 취소 하거나 삭제 "x" 아이콘을 클릭 합니다. 각 리소스에 대해 원하는 할당량을 입력 한 후 클릭 **저장 및 계속** 지원 요청 만들기를 계속 진행 하려면 할당량 세부 정보 패널에 있습니다.

    ![새 제한](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>구독 사용 하 여 수준의 네트워킹 할당량 증가 요청 **사용량 + 할당량** 블레이드

Azure의 '사용량 + 할당량'를 통해 지원 요청을 만들려면 사용 하 여 아래 지침에 따라 블레이드에서 Azure portal에서 사용할 수 있습니다. 

1. https://portal.azure.com 에서 **구독**을 선택합니다.

    ![구독](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

    ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

    ![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

    ![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5. 3 단계부터 단계를 수행 합니다 *구독 수준에서 할당량 증가 요청 네트워킹* 사용 하 여 섹션을 **도움말 + 지원** 블레이드에서 섹션

## <a name="about-networking-limits"></a>네트워킹 제한에 대 한

네트워킹 제한에 대 한 자세한 내용은 참조는 [네트워킹 섹션](../azure-subscription-service-limits.md#networking-limits) 제한 페이지나 네트워크 제한 FAQ