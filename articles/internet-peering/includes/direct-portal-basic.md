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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774641"
---
1. **리소스** > **만들기를 클릭합니다 모두 보기.**

    > [!div class="mx-imgBorder"]
    > ![피어링 검색](../media/setup-seeall.png)

1. 검색 상자에서 *피어링을* 검색하고 키보드에서 *Enter를* 누르십시오. 결과에서 **피어링** 리소스를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 시작](../media/setup-launch.png)

1. **피어링이** 시작되면 페이지를 검토하여 세부 정보를 확인합니다. 준비가 되면 을 **만들기를 클릭합니다.**

    > [!div class="mx-imgBorder"]
    > ![피어링 만들기](../media/setup-create.png)

1. **피어링 만들기** 페이지에서 기본 탭 아래에 아래와 같이 필드를 **입력합니다.**

    > [!div class="mx-imgBorder"]
    > ![피어링 기본 사항](../media/setup-basics-tab.png)

    * Azure **구독을**선택합니다.
    * **리소스 그룹의**경우 드롭다운에서 기존 리소스 그룹을 선택하거나 **새 만들기를**클릭하여 새 그룹을 만들 수 있습니다. 이 예제에 대 한 새 리소스 그룹을 만듭니다.
    * **이름은** 리소스 이름에 해당하며 원하는 모든 이름을 지정할 수 있습니다.
    * 위의 단계에서 기존 리소스 그룹을 선택한 경우 **영역이** 자동으로 선택됩니다. 새 리소스 그룹을 만들도록 선택한 경우 리소스가 상주할 Azure 지역도 선택해야 합니다. 미국 동부

        > [!NOTE]
        > 리소스 그룹이 있는 지역은 Microsoft를 사용하여 피어링을 만들려는 위치와 독립적입니다. 그러나 가장 가까운 Azure 지역에 있는 리소스 그룹 내에서 피어링 리소스를 구성하는 것이 좋습니다. 예: 애쉬번의 피어링의 경우 *미국 동부* 또는 *US2 동부에서* 리소스 그룹을 만들 수 있습니다.

    * 피어 ASN 필드에서 **ASN을** 선택합니다.

        > [!IMPORTANT]
        > * 피어링 요청을 제출하기 전에 유효성 검사상태를 "승인됨"으로만 ASN을 선택할 수 있습니다. PeerAsn 요청을 제출한 경우 ASN 연결이 "승인"될 때까지 12시간 정도 기다립니다. 선택한 ASN이 유효성 검사를 보류 중인 경우 오류 메시지가 표시됩니다. 
        > * 선택해야 하는 ASN이 표시되지 않으면 올바른 구독을 선택했는지 확인합니다. 그렇다면 [피어 ASN 연결 사용자](../howto-subscription-association-portal.md)구독을 사용하여 PeerAsn을 이미 만들었는지 확인합니다.

        > [!div class="mx-imgBorder"]
        > ![피어링 기본 사항 가득](../media/setup-direct-basics-filled-tab.png)

    * **다음을 클릭합니다: 구성 >** 계속합니다.
