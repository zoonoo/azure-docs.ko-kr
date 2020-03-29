---
title: 네트워킹 제한 증가 | 마이크로 소프트 문서
description: 네트워킹 제한 증가
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9b5c7043b06172c2d4931ca1c3fd3ac5d0e80883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547808"
---
# <a name="networking-limit-increase"></a>네트워킹 제한 증가

Azure [포털을](https://portal.azure.com) 사용하여 네트워킹 할당량을 늘립니다.

Azure 포털에서 현재 네트워킹 사용량 및 할당량을 보려면 구독을 연 다음 **사용량 + 할당량을 선택합니다.** 다음 옵션을 사용하여 네트워크 사용량 및 제한을 볼 수도 있습니다.

* [사용 CLI](/cli/azure/network#az-network-list-usages)
* [Powershell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [네트워크 사용량 API](/rest/api/virtualnetwork/virtualnetworks/listusage)

**도움말 + 지원을** 사용하거나 포털의 **사용량 + 할당량을** 사용하여 증가를 요청할 수 있습니다.

> [!Note]
> **공용 IP 접두정의**기본 크기를 변경하려면 드롭다운 목록에서 **공용 IP 인터네트워크 접두사 길이를** 선택합니다.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>도움말 + 지원을 사용하여 구독 수준에서 네트워킹 할당량 증가 요청

아래 지침에 따라 Azure 포털에서 **도움말 + 지원을** 사용하여 지원 요청을 만듭니다.

1. [Azure 포털에](https://portal.azure.com)로그인한 다음 Azure 포털 메뉴에서 **도움말 + 지원을** 선택하거나 도움말 + **지원을**검색하고 선택합니다.

    ![도움말 + 지원](./media/networking-quota-request/help-plus-support.png)

1. **새 지원 요청**을 선택합니다.

    ![새 지원 요청](./media/networking-quota-request/new-support-request.png)

1. **문제 유형의**경우 **서비스 및 구독 한도(할당량)를 선택합니다.**

    ![문제 유형 드롭다운에서 구독 제한 선택](./media/networking-quota-request/select-quota-issue-type.png)

1. 할당량을 늘려야 하는 구독을 선택합니다.

    ![구독 newSR 선택](./media/networking-quota-request/select-subscription-support-request.png)

1. **할당량 유형에서** **네트워킹을**선택합니다. **다음: 솔루션**을 선택합니다.

    ![할당량 유형 선택](./media/networking-quota-request/select-quota-type-network.png)

1. **문제 세부 정보에서** **세부 정보 제공을** 선택하고 요청을 처리하는 데 도움이 되는 추가 정보를 입력합니다.

    ![세부 정보 제공](./media/networking-quota-request/provide-details-link.png)

1. 할당량 **세부 정보** 패널에서 배포 모델, 위치 및 요청에 포함할 리소스를 선택합니다.

    ![할당량 세부 정보 DM](./media/networking-quota-request/quota-details-network.png)

1. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 **리소스** 메뉴에서 리소스를 선택 취소하거나 "x" 삭제 아이콘을 선택합니다. 각 리소스에 대한 할당량을 입력한 후 **저장을** 선택하고 지원 요청 만들기를 계속합니다.

    ![새 제한](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>사용량 + 할당량을 사용하여 구독 수준에서 네트워킹 할당량 증가 요청

Azure 포털에서 **사용량 + 할당량을** 사용하여 지원 요청을 만들려면 다음 지침을 따릅니다.

1. https://portal.azure.com에서 구독을 검색하고 **선택합니다.**

    ![Subscriptions](./media/networking-quota-request/search-for-suscriptions.png)

1. 할당량을 늘려야 하는 구독을 선택합니다.

    ![구독 선택](./media/networking-quota-request/select-subscription-change-quota.png)

1. **사용량 + 할당량** 선택

    ![사용량 + 할당량 선택](./media/networking-quota-request/select-usage-plus-quotas.png)

1. 오른쪽 상단 모서리에서 **증가 요청을**선택합니다.

    ![증가 요청](./media/networking-quota-request/request-increase-from-subscription.png)

1. 구독 수준에서 네트워킹 할당량 [증가를 요청에서](#request-networking-quota-increase-at-subscription-level-using-help--support)3단계로 시작하는 단계를 따릅니다.

## <a name="about-networking-limits"></a>네트워킹 제한 정보

네트워킹 제한에 대한 자세한 내용은 제한 페이지의 [네트워킹 섹션](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) 또는 네트워크 제한 FAQ를 참조하십시오.
