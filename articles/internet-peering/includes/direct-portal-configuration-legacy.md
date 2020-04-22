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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678863"
---
1. **피어링 만들기** 페이지에서 **구성** 탭에서 여기에 표시된 대로 상자를 입력합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 페이지 구성 탭 만들기](../media/setup-direct-conf-tab.png)

    * **피어링 유형의**경우 직접 을 **선택합니다.**
    * **Microsoft 네트워크의**경우 **AS8075를**선택합니다. ASN 8069를 선택하지 마십시오. 그것은 특별한 응용 프로그램을 위해 예약되어 있으며 [마이크로 소프트 피어링에서만](mailto:peering@microsoft.com)사용됩니다.
    * **SKU를** **기본 무료로 선택합니다.** 프리미엄 무료는 특별 애플리케이션용으로 예약되어 있으므로 선택하지 마십시오.
    * 피어링을 Azure 리소스로 변환할 **메트로** 위치를 선택합니다. Azure 리소스로 변환되지 않은 선택한 **메트로** 위치에서 Microsoft와의 피어링 연결이 있는 경우 이러한 연결은 표시된 대로 **피어링 연결** 섹션에 나열됩니다. 이제 이러한 피어링 연결을 Azure 리소스로 변환할 수 있습니다.

        > [!div class="mx-imgBorder"]
        > ![피어링 연결 목록](../media/setup-directlegacy-conf-tab.png)

1. 대역폭을 업데이트해야 하는 경우 회선의 편집 버튼을 선택하여 연결 설정을 수정합니다.

    > [!div class="mx-imgBorder"]
    > ![편집 단추](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 선택한 **메트로** 위치에서 Microsoft와 피어링 연결을 추가하려면 **새 만들기를**선택합니다. 자세한 내용은 [포털을 사용하여 직접 피어링 만들기 또는 수정을](../howto-direct-portal.md)참조하십시오.
    >

1. **검토 + 만들기**를 선택합니다. 포털은 입력한 정보의 기본 유효성 검사를 실행합니다. 상단의 리본에는 최종 *유효성 검사 실행*메시지가 표시됩니다. .

    > [!div class="mx-imgBorder"]
    > ![피어링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사로*변경된 후 전달된 정보를 확인합니다. [만들기]를 **Create**선택하여 요청을 제출합니다. 요청을 수정하려면 **이전** 단계를 선택하고 반복합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 제출](../media/setup-direct-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하십시오. 여기에 표시된 대로 성공적인 배포가 나타납니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 성공](../media/setup-direct-success.png)
