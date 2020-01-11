---
title: 네트워킹 제한 증가 | Microsoft Docs
description: 네트워킹 제한 증가
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 04a22d987e27f054648637890fbd61c56533c2ee
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896806"
---
# <a name="networking-limit-increase"></a>네트워킹 제한 증가

현재 네트워킹 사용 및 할당량을 보려면 Azure Portal에서 사용 **+ 할당량** 블레이드를 방문 합니다. 사용 [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) 또는 [네트워크 사용 API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) 를 사용 하 여 네트워크 사용량 및 제한을 볼 수도 있습니다.

포털에서 **도움말 + 지원** 블레이드 또는 **사용량 + 할당량** 블레이드를 통해 증가를 요청할 수 있습니다.

> [!Note]
> 공용 IP 접두사의 기본 크기를 변경 하려면 드롭다운 목록에서 "최소 공용 IP 인터네트워크 접두사 길이"를 선택 합니다.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>**도움말 + 지원** 블레이드를 사용 하 여 구독 수준에서 네트워킹 할당량 증가 요청

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 도움말 + 지원 ' 블레이드를 통해 지원 요청을 만듭니다. 

1. [https://portal.azure.com](https://portal.azure.com )을 선택 **도움말 + 지원**합니다.

    ![도움말 + 지원](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  **새 지원 요청**을 선택합니다. 

    ![새 지원 요청](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 문제 유형 드롭다운에서 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.

    ![문제 유형 드롭다운](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 할당량을 늘려야 하는 구독을 선택합니다.

    ![구독 newSR 선택](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. **할당량 유형** 드롭다운에서 **네트워킹** 을 선택 합니다. 

    ![할당량 유형 선택](./media/networking-quota-request/select-quota-type-network.png)

6. **문제 세부**정보에서 **세부 정보 제공**을 클릭 하 여 요청을 처리 하는 데 도움이 되는 추가 정보를 제공 합니다.

    ![세부 정보 제공](./media/resource-manager-core-quotas-request/provide-details.png)

7. **할당량 정보** 패널에서 배포 모델, 위치 및 증가를 요청 하려는 리소스를 선택 합니다.

    ![할당량 정보 DM](./media/networking-quota-request/quota-details-network.png)

8.  구독에 대한 새 한도를 입력합니다. 줄을 제거 하려면 리소스 드롭다운에서 리소스를 선택 취소 하거나 삭제 "x" 아이콘을 클릭 합니다. 각 리소스에 대해 원하는 할당량을 입력 한 후 할당량 정보 패널에서 **저장을 클릭 하 고 계속** 을 클릭 하 여 지원 요청 만들기를 계속 합니다.

    ![새 제한](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>**사용량 + 할당량** 블레이드를 사용 하 여 구독 수준에서 네트워킹 할당량 증가 요청

아래 지침에 따라 Azure Portal에서 제공 하는 Azure의 ' 사용 + 할당량 ' 블레이드를 통해 지원 요청을 만듭니다. 

1. https://portal.azure.com 에서 **구독**을 선택합니다.

    ![구독](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

    ![구독 선택](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

    ![사용량 + 할당량 선택](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

    ![증가 요청](./media/resource-manager-core-quotas-request/request-increase.png)

5. **도움말 + 지원** 블레이드 섹션을 사용 하 여 *네트워킹 할당량 증가 요청* 섹션에서 3 단계부터 시작 하는 단계를 수행 합니다.

## <a name="about-networking-limits"></a>네트워킹 제한 정보

네트워킹 한도에 대해 자세히 알아보려면 제한 페이지의 [네트워킹 섹션](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) 또는 네트워크 제한 FAQ를 참조 하세요.
