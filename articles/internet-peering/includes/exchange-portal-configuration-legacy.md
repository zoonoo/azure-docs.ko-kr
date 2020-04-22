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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678521"
---
1. **피어링 만들기** 페이지에서 **구성** 탭에서 여기에 표시된 대로 상자를 입력합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 페이지 만들기 교환 피어링 유형](../media/setup-exchange-conf-tab.png)

    * **피어링 유형의**경우 **Exchange**를 선택합니다.
    * **SKU를** **기본 무료로 선택합니다.**
    * 피어링을 Azure 리소스로 변환할 **메트로** 위치를 선택합니다. Azure 리소스로 변환되지 않은 선택한 **메트로** 위치에서 Microsoft와의 피어링 연결이 있는 경우 이러한 연결은 표시된 대로 **피어링 연결** 섹션에 나열됩니다. 이제 이러한 피어링 연결을 Azure 리소스로 변환할 수 있습니다.

        > [!div class="mx-imgBorder"]
        > ![피어링 연결 목록](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > 레거시 피어링 연결에 대한 설정은 수정할 수 없습니다. 선택한 **메트로** 위치에서 Microsoft와 피어링 연결을 추가하려면 **새 만들기를**선택합니다. 자세한 내용은 [포털을 사용하여 Exchange 피어링 만들기 또는 수정을](../howto-exchange-portal.md)참조하십시오.
        >

1. **검토 + 만들기**를 선택합니다. 포털은 입력한 정보의 기본 유효성 검사를 실행합니다. 상단의 리본에는 최종 *유효성 검사 실행*메시지가 표시됩니다. .

    > [!div class="mx-imgBorder"]
    > ![피어링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사로*변경된 후 전달된 정보를 확인합니다. [만들기]를 **Create**선택하여 요청을 제출합니다. 요청을 수정해야 하는 경우 **이전** 단계를 선택하고 반복합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 제출](../media/setup-exchange-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하십시오. 성공적인 배포가 그림과 같이 나타납니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 성공](../media/setup-direct-success.png)
