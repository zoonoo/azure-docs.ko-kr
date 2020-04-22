---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678560"
---
1. **리소스** > 만들기 선택**모두 보기**

    > [!div class="mx-imgBorder"]
    > ![피어링 검색](../media/setup-seeall.png)

1. 검색 상자에서 **피어링을** 검색하고 키보드에서 **입력을** 선택합니다. 결과에서 **피어링** 리소스를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 시작](../media/setup-launch.png)

1. **피어링이** 시작된 후 페이지를 검토하여 세부 정보를 이해합니다. 준비가 되면 에서 **만들기를**선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 만들기](../media/setup-create.png)

1. **피어링 만들기** 페이지에서 기본 탭에서 여기에 표시된 대로 상자를 **입력합니다.**

    > [!div class="mx-imgBorder"]
    > ![피어링 기본 탭](../media/setup-basics-tab.png)

    * Azure **구독을**선택합니다.
    * **리소스 그룹의**경우 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새 만들기를**선택하여 새 그룹을 만들 수 있습니다. 이 예제에 대 한 새 리소스 그룹을 만듭니다.
    * **이름은** 리소스 이름에 해당하며 원하는 모든 이름을 지정할 수 있습니다.
    * 기존 리소스 그룹을 선택한 경우 **지역이** 자동으로 선택됩니다. 새 리소스 그룹을 만들도록 선택한 경우 리소스가 상주할 Azure 지역도 선택해야 합니다.

        > [!NOTE]
        > 리소스 그룹이 있는 지역은 Microsoft를 사용하여 피어링을 만들려는 위치와 독립적입니다. 그러나 가장 가까운 Azure 지역에 있는 리소스 그룹 내에서 피어링 리소스를 구성하는 것이 좋습니다. 예를 들어 애쉬번의 피어링의 경우 미국 동부 또는 US2 동부에서 리소스 그룹을 만들 수 있습니다.

    * 피어 ASN 상자에서 **ASN을** 선택합니다.

        > [!IMPORTANT]
        > * 피어링 요청을 제출하기 전에 유효성 검사 상태가 승인된 ASN만 선택할 수 있습니다. PeerAsn 요청을 제출한 경우 ASN 연결이 승인될 때까지 12시간 정도 기다립니다. 선택한 ASN이 유효성 검사를 보류 중인 경우 오류 메시지가 표시됩니다. 
        > * 선택해야 하는 ASN이 표시되지 않으면 올바른 구독을 선택했는지 확인합니다. 그렇다면 [피어 ASN 연결 부어도서 구독을](../howto-subscription-association-portal.md)사용하여 PeerAsn을 이미 만들었는지 확인합니다.

        > [!div class="mx-imgBorder"]
        > ![피어링 기본 사항](../media/setup-direct-basics-filled-tab.png)

    * **다음 : 구성 >** 선택합니다.
