---
title: 파일 포함
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678560"
---
1.  >  **리소스 만들기를 선택 하**고**모두 보기**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 검색](../media/setup-seeall.png)

1. 검색 상자에서 **피어 링** 을 검색 하 고 키보드에서 **Enter 키** 를 선택 합니다. 결과에서 **피어 링** 리소스를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 시작](../media/setup-launch.png)

1. **피어 링** 이 시작 되 면 페이지를 검토 하 여 세부 정보를 이해 합니다. 준비가 되 면 **만들기**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 만들기](../media/setup-create.png)

1. **피어 링 만들기** 페이지의 **기본 사항** 탭에서 여기에 표시 된 대로 상자를 입력 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 기본 탭](../media/setup-basics-tab.png)

    * Azure **구독**을 선택 합니다.
    * **리소스 그룹**의 경우 드롭다운 목록에서 기존 리소스 그룹을 선택 하거나 **새로 만들기**를 선택 하 여 새 그룹을 만들 수 있습니다. 이 예에서는 새 리소스 그룹을 만듭니다.
    * **이름은** 리소스 이름에 해당 하며, 선택 하는 것이 될 수 있습니다.
    * 기존 리소스 그룹을 선택한 경우 **지역이** 자동으로 선택 됩니다. 새 리소스 그룹을 만들도록 선택한 경우에는 리소스를 저장할 Azure 지역도 선택 해야 합니다.

        > [!NOTE]
        > 리소스 그룹이 있는 지역은 Microsoft와 피어 링을 만들려는 위치에 독립적입니다. 하지만 가장 가까운 Azure 지역에 상주 하는 리소스 그룹 내에서 피어 링 리소스를 구성 하는 것이 좋습니다. 예를 들어 Ashburn 위한 피어 링의 경우 미국 동부 또는 동부 미국에서 리소스 그룹을 만들 수 있습니다.

    * **피어 asn** 상자에서 ASN을 선택 합니다.

        > [!IMPORTANT]
        > * 피어 링 요청을 제출 하기 전에 승인 된 대로 ValidationState를 사용 하 여 ASN만 선택할 수 있습니다. PeerAsn 요청을 방금 제출한 경우 12 시간 동안 기다리거나 ASN 연결을 승인 합니다. 선택한 ASN이 유효성 검사 보류 중인 경우 오류 메시지가 표시 됩니다. 
        > * 선택 해야 하는 ASN이 표시 되지 않는 경우 올바른 구독을 선택 했는지 확인 합니다. 그렇다면 [피어 asn을 Azure 구독에 연결](../howto-subscription-association-portal.md)을 사용 하 여 peerasn을 이미 만들었는지 확인 합니다.

        > [!div class="mx-imgBorder"]
        > ![피어 링 기본 정보 입력](../media/setup-direct-basics-filled-tab.png)

    * **다음: 구성 >** 를 선택 하 여 계속 합니다.
