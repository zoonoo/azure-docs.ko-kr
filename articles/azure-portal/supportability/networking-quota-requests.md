---
title: 네트워킹 제한 증가
description: 네트워킹 제한 증가
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 801f0424e7ec15fbde58f35975f4c7eca4c9a5de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775568"
---
# <a name="networking-limit-increase"></a>네트워킹 제한 증가

[Azure Portal](https://portal.azure.com)을 사용하여 네트워킹 할당량을 늘립니다.

Azure Portal에서 현재 네트워킹 사용량 및 할당량을 보려면 구독을 연 다음 **사용량 + 할당량** 을 선택합니다. 다음 옵션을 사용하여 네트워크 사용량 및 제한을 확인할 수도 있습니다.

* [사용량 CLI](/cli/azure/network#az_network_list_usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [네트워크 사용량 API](/rest/api/virtualnetwork/virtualnetworks/listusage)

**도움말 + 지원** 을 사용하거나 포털의 **사용량 + 할당량** 에서 증가를 요청할 수 있습니다.

> [!Note]
> **공개 IP 접두사** 의 기본 크기를 변경하려면 드롭다운 목록에서 **최소 공용 IP 네트워크 간 접두사 길이** 를 선택합니다.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>도움말 + 지원을 사용하여 구독 수준에서 네트워킹 할당량 증가 요청

아래 지침에 따라 Azure Portal에서 **도움말 + 지원** 을 사용하여 지원 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음 Azure Portal 메뉴에서 **도움말 + 지원** 을 선택하거나 **도움말 + 지원** 을 검색하여 선택합니다.

    ![도움말 + 지원](./media/networking-quota-request/help-plus-support.png)

1. **새 지원 요청** 을 선택합니다.

    ![새 지원 요청](./media/networking-quota-request/new-support-request.png)

1. **문제 유형** 에 대해 **서비스 및 구독 제한(할당량)** 을 선택합니다.

    ![문제 유형 드롭다운에서 구독 제한 선택](./media/networking-quota-request/select-quota-issue-type.png)

1. 할당량을 늘려야 하는 구독을 선택합니다.

    ![구독 newSR 선택](./media/networking-quota-request/select-subscription-support-request.png)

1. **할당량 유형** 에서 **네트워킹** 을 선택합니다. **다음: 솔루션** 을 선택합니다.

    ![할당량 유형 선택](./media/networking-quota-request/select-quota-type-network.png)

1. **문제 세부 정보** 에서 **세부 정보 제공** 을 선택하고 요청을 처리하는 데 도움이 되는 추가 정보를 입력합니다.

    ![세부 정보 제공](./media/networking-quota-request/provide-details-link.png)

1. **할당량 세부 정보** 패널에서 배포 모델, 위치 및 요청에 포함할 리소스를 선택합니다.

    ![할당량 세부 정보 DM](./media/networking-quota-request/quota-details-network.png)

1. 구독에 대한 새 한도를 입력합니다. 줄을 제거하려면 **리소스** 메뉴에서 리소스를 선택 취소하거나 "x" 삭제 아이콘을 선택합니다. 각 리소스에 대한 할당량을 입력한 후 **저장 및 계속** 을 선택하여 지원 요청 생성을 계속합니다.

    ![새 제한](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>사용량 + 할당량을 사용하여 구독 수준에서 네트워킹 할당량 증가 요청

다음 지침에 따라 Azure Portal에서 **사용량 + 할당량** 을 사용하여 지원 요청을 만듭니다.

1. https://portal.azure.com 에서 **구독** 을 검색하여 선택합니다.

    ![Subscriptions](./media/networking-quota-request/search-for-suscriptions.png)

1. 할당량을 늘려야 하는 구독을 선택합니다.

    ![구독 선택](./media/networking-quota-request/select-subscription-change-quota.png)

1. **사용량 + 할당량** 을 선택합니다.

    ![사용량 + 할당량 선택](./media/networking-quota-request/select-usage-plus-quotas.png)

1. 오른쪽 위 모서리에서 **증가 요청** 을 선택합니다.

    ![증가 요청](./media/networking-quota-request/request-increase-from-subscription.png)

1. [구독 수준에서 네트워킹 할당량 증가 요청](#request-networking-quota-increase-at-subscription-level-using-help--support)의 3단계부터 시작하는 단계를 따릅니다.

## <a name="about-networking-limits"></a>네트워킹 제한 정보

네트워킹 제한에 대한 자세한 내용은 제한 페이지 또는 네트워크 제한 FAQ의 [네트워킹 섹션](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.
