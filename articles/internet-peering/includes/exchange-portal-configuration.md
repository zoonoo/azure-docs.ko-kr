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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680923"
---
1. **피어링 만들기** 페이지의 **구성** 탭에서 표시된 것과 같이 상자를 채웁니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 페이지 Exchange 피어링 유형 만들기](../media/setup-exchange-conf-tab.png)

    * **피어링 유형** 으로 **Exchange** 를 선택합니다.
    * **기본 무료** 로 **SKU** 를 선택합니다.
    * 피어링을 설정하려는 **메트로** 위치를 선택합니다.

        > [!NOTE]
        > 선택한 **메트로** 위치에서 Microsoft와의 피어링 연결이 이미 있고 포털을 처음 사용하여 해당 위치에서 피어링을 설정하는 경우 기존 피어링 연결이 표시된 것과 같이 **피어링 연결** 섹션에 나열됩니다. 새 연결과 함께 모든 연결을 한 곳에서 관리할 수 있도록 Microsoft는 이러한 피어링 연결을 Azure 리소스로 자동 변환합니다. 자세한 내용은 [포털을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](../howto-legacy-exchange-portal.md)을 참조하세요.
        >

1. **피어링 연결** 에서 **새로 만들기** 를 선택하여 설정하려는 각 새 연결에 대한 줄을 추가합니다.

    * 연결 설정을 구성하거나 수정하려면 줄에 대한 편집 단추를 선택합니다.

        > [!div class="mx-imgBorder"]
        > ![편집 단추](../media/setup-exchange-conf-tab-edit.png)

    * 줄을 삭제하려면 **...**  > **삭제** 를 선택합니다.

        > [!div class="mx-imgBorder"]
        > ![삭제 단추](../media/setup-exchange-conf-tab-delete.png)

    * 여기에 표시된 것처럼 연결에 대한 모든 설정을 제공해야 합니다.

         > [!div class="mx-imgBorder"]
         > ![Exchange 피어링 연결 페이지](../media/setup-exchange-conf-tab-connection.png)

        1. 연결을 설정해야 하는 **피어링 시설** 을 선택합니다.
        1. **IPv4 주소** 및 **IPv6 주소** 상자에서 neightbor 명령을 사용하여 Microsoft 라우터에 구성할 IPv4 및 IPv6 주소를 각각 입력합니다.
        1. **최대 보급 IPv4 주소** 및 **최대 보급 IPv6 주소** 상자에 각각 보급할 IPv4 및 IPv6 접두사 수를 입력합니다.
        1. **확인** 을 선택하여 연결 설정을 저장합니다.

1. 단계를 반복하여 이전에 선택한 **메트로** 내에서 Microsoft가 네트워크와 함께 배치된 시설에서 연결을 더 추가합니다.

1. 필요한 모든 연결을 추가한 후 **검토 + 만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 구성 탭](../media/setup-exchange-conf-tab-final.png)

1. 포털에서 입력한 정보의 기본 유효성 검사를 실행합니다. 맨 위에 있는 리본에는 *최종 유효성 검사 실행 중...* 이라는 메시지가 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사 통과* 로 변경되면 정보를 확인합니다. **만들기** 를 선택하여 요청을 제출합니다. 요청을 수정하려면 **이전** 을 선택하고 단계를 반복합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 제출](../media/setup-exchange-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하세요. 성공적으로 배포되면 다음과 같이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 성공](../media/setup-direct-success.png)
