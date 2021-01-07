---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83846169"
---
1. **리소스 만들기** > **모두 표시**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 검색](../media/setup-seeall.png)

1. 검색 상자에서 **피어링**을 검색하고 키보드에서 **Enter** 키를 선택합니다. 결과에서 **피어링** 리소스를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 시작](../media/setup-launch.png)

1. **피어링**이 시작된 후에는 페이지를 검토하여 세부 정보를 이해합니다. 준비가 되면 **만들기**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 만들기](../media/setup-create.png)

1. **피어링 만들기** 페이지의 **기본 사항** 탭에서 다음과 같이 입력란을 채웁니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 기본 사항 탭](../media/setup-basics-tab.png)

    * Azure **구독**을 선택합니다.
    * **리소스 그룹**의 경우 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기**를 선택하여 새 그룹을 만들 수 있습니다. 이 예제에서는 새 리소스 그룹을 만듭니다.
    * **이름**은 리소스 이름에 해당하며, 원하는 어떤 이름도 사용 가능합니다.
    * 기존 리소스 그룹을 선택한 경우 **지역**이 자동으로 선택됩니다. 새 리소스 그룹을 만들도록 선택한 경우에는 리소스를 저장할 Azure 지역도 선택해야 합니다.

        > [!NOTE]
        > 리소스 그룹이 있는 지역은 Microsoft와 피어링을 만들려는 위치와 별개입니다. 하지만 가장 가까운 Azure 지역에 있는 리소스 그룹 내에 피어링 리소스를 구성하는 것이 좋습니다. 예를 들어, 애슈번에서 피어링이 필요한 경우 미국 동부 또는 미국 동부 2에 리소스 그룹을 만들 수 있습니다.

    * **피어 ASN** 상자에서 ASN을 선택합니다.

        > [!IMPORTANT]
        > * 피어링 요청을 제출하기 전에 ValidationState가 Approved인 ASN만 선택할 수 있습니다. 방금 PeerAsn 요청을 제출한 경우 ASN 연결이 승인될 때까지 12시간 정도 기다립니다. 선택한 ASN의 유효성 검사가 보류 중인 경우 오류 메시지가 표시됩니다. 
        > * 선택해야 하는 ASN이 표시되지 않는 경우 올바른 구독을 선택했는지 확인합니다. 그렇다면 [피어 ASN을 Azure 구독에 연결](../howto-subscription-association-portal.md)을 사용하여 PeerAsn을 이미 만들었는지 확인합니다.

        > [!div class="mx-imgBorder"]
        > ![피어링 기본 정보가 입력됨](../media/setup-direct-basics-filled-tab.png)

    * **다음: 구성 >** 을 선택하여 계속 진행합니다.
