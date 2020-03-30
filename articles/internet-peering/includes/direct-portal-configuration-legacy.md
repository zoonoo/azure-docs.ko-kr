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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775070"
---
1. **피어링 만들기** 페이지에서 **구성** 탭 아래에 아래와 같이 필드를 입력합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 구성 - 직접](../media/setup-direct-conf-tab.png)

    * **피어링 유형의**경우 직접 을 *선택합니다.*
    * **Microsoft 네트워크의**경우 *AS8075를*선택합니다. ASN 8069를 선택하지 마십시오. 그것은 특별한 응용 프로그램을 위해 예약되어 있으며 [마이크로 소프트 피어링에서만](mailto:peering@microsoft.com)사용됩니다.
    * **SKU를** *기본 무료로 선택합니다.* 프리미엄 *무료는* 특별 신청을 위해 예약되어 있으므로 선택하지 마십시오.
    * 피어링을 Azure 리소스로 변환할 위치에 대한 **메트로** 위치를 선택합니다. 선택한 **메트로** 위치에서 Azure 리소스로 변환되지 않은 피어링 연결이 있는 경우 이러한 연결은 아래와 같이 **피어링 연결** 섹션에 나열됩니다. 이제 이러한 피어링 연결을 Azure 리소스로 변환할 수 있습니다.

        > [!div class="mx-imgBorder"]
        > ![피어링 구성 - 직접 - 레거시 연결](../media/setup-directlegacy-conf-tab.png)

1. 대역폭을 업데이트해야 하는 경우 아래 강조 표시된 것처럼 줄의 편집 단추를 클릭하여 연결 설정을 수정합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 구성 - 직접 편집](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 선택한 **메트로** 위치에 Microsoft와 피어링 연결을 추가하려면 새 단추 **만들기를** 클릭하여 추가할 수 있습니다. 자세한 내용은 [포털을 사용하여 직접 피어링 만들기 또는 수정을](../howto-direct-portal.md) 참조하십시오.
    >

1. 검토를 클릭 **+ 만들기**. 포털이 입력한 정보의 기본 유효성 검사를 실행하는지 확인합니다. 이 표시 는 맨 위에 리본에 표시 됩니다., *실행 최종 유효성 검사...*

    > [!div class="mx-imgBorder"]
    > ![피어링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 유효성 검사 전달으로 전환한 후 정보를 확인하고 **만들기를**클릭하여 요청을 *제출합니다.* 요청을 수정해야 하는 경우 **이전을** 클릭하고 위의 단계를 반복합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 제출](../media/setup-direct-review-tab-submit.png)

1. 요청을 제출하면 배포가 완료될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하십시오. 성공적인 배포는 아래와 같이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 성공](../media/setup-direct-success.png)
