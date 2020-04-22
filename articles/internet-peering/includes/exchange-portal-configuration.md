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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680923"
---
1. **피어링 만들기** 페이지에서 **구성** 탭에서 표시된 대로 상자를 입력합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 페이지 만들기 교환 피어링 유형](../media/setup-exchange-conf-tab.png)

    * **피어링 유형의**경우 **Exchange**를 선택합니다.
    * **SKU를** **기본 무료로 선택합니다.**
    * 피어링을 설정할 **지하철** 위치를 선택합니다.

        > [!NOTE]
        > 선택한 **Metro** 위치에서 Microsoft와 피어링 연결이 이미 있고 포털을 처음으로 사용하여 해당 위치에서 피어링을 설정하는 경우 기존 피어링 연결이 표시된 대로 **피어링 연결** 섹션에 나열됩니다. Microsoft는 이러한 피어링 연결을 Azure 리소스로 자동 변환하여 한 곳에서 새 연결과 함께 모두 관리할 수 있도록 합니다. 자세한 내용은 [포털 을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환을](../howto-legacy-exchange-portal.md)참조하세요.
        >

1. **피어링 연결에서** **새 만들기를** 선택하여 설정하려는 각 새 연결에 대해 선을 추가합니다.

    * 연결 설정을 구성하거나 수정하려면 줄의 편집 단추를 선택합니다.

        > [!div class="mx-imgBorder"]
        > ![편집 단추](../media/setup-exchange-conf-tab-edit.png)

    * 선을 삭제하려면 **...**  >  **삭제합니다.**

        > [!div class="mx-imgBorder"]
        > ![삭제 단추](../media/setup-exchange-conf-tab-delete.png)

    * 여기에 표시된 대로 연결에 대한 모든 설정을 제공해야 합니다.

         > [!div class="mx-imgBorder"]
         > ![교환 피어링 연결 페이지](../media/setup-exchange-conf-tab-connection.png)

        1. 연결을 설정해야 하는 **피어링 기능을** 선택합니다.
        1. **IPv4 주소** 및 **IPv6 주소** 상자에서 이웃 명령을 사용하여 Microsoft 라우터에서 구성되는 IPv4 및 IPv6 주소를 각각 입력합니다.
        1. 광고된 **최대 IPv4 주소와 최대** **광고된 IPv6 주소** 상자에 광고할 IPv4 및 IPv6 접두사 수를 각각 입력합니다.
        1. 연결 설정을 저장하려면 **확인을** 선택합니다.

1. 이 단계를 반복하여 Microsoft가 이전에 선택한 **메트로** 내에서 네트워크와 동위치가 있는 모든 시설에서 더 많은 연결을 추가합니다.

1. 필요한 모든 연결을 추가한 후 **검토 + 만들기를**선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 구성 탭](../media/setup-exchange-conf-tab-final.png)

1. 포털은 입력한 정보의 기본 유효성 검사를 실행합니다. 상단의 리본에는 최종 *유효성 검사 실행*메시지가 표시됩니다. .

    > [!div class="mx-imgBorder"]
    > ![피어링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사로*변경된 후 전달된 정보를 확인합니다. [만들기]를 **Create**선택하여 요청을 제출합니다. 요청을 수정하려면 **이전** 단계를 선택하고 반복합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 제출](../media/setup-exchange-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하십시오. 여기에 표시된 대로 성공적인 배포가 나타납니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 성공](../media/setup-direct-success.png)
