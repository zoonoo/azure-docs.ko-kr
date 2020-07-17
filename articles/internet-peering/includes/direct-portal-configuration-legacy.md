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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81678863"
---
1. **피어 링 만들기** 페이지의 **구성** 탭에서 여기에 표시 된 대로 상자를 입력 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 페이지 구성 탭 만들기](../media/setup-direct-conf-tab.png)

    * **피어 링 유형**으로 **Direct**를 선택 합니다.
    * **Microsoft 네트워크**에서 **AS8075**를 선택 합니다. ASN 8069을 선택 하지 마세요. 특수 응용 프로그램용으로 예약 되어 있으며 [Microsoft 피어 링](mailto:peering@microsoft.com)에서만 사용 됩니다.
    * **기본 무료**로 **SKU** 를 선택 합니다. 특수 응용 프로그램용으로 예약 되어 있으므로 프리미엄 무료를 선택 하지 마세요.
    * 피어 링을 Azure 리소스로 변환 하려는 **Metro** 위치를 선택 합니다. Azure 리소스로 변환 되지 않은 선택한 **Metro** 위치에서 Microsoft와 피어 링 연결을 사용 하는 경우 이러한 연결은 다음과 같이 **피어 링 연결** 섹션에 나열 됩니다. 이제 이러한 피어 링 연결을 Azure 리소스로 변환할 수 있습니다.

        > [!div class="mx-imgBorder"]
        > ![피어 링 연결 목록](../media/setup-directlegacy-conf-tab.png)

1. 대역폭을 업데이트 해야 하는 경우 연결 설정을 수정 하는 줄에 대 한 편집 단추를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![편집 단추](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 선택한 **Metro** 위치에 Microsoft와 피어 링 연결을 추가 하려면 **새로 만들기**를 선택 합니다. 자세한 내용은 [포털을 사용 하 여 직접 피어 링 만들기 또는 수정](../howto-direct-portal.md)을 참조 하세요.
    >

1. **검토 + 만들기**를 선택합니다. 포털에서 입력 한 정보에 대 한 기본 유효성 검사를 실행 합니다. 맨 위에 있는 리본은 *최종 유효성 검사를 실행*하는 메시지를 표시 합니다 ....

    > [!div class="mx-imgBorder"]
    > ![피어 링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사 성공*으로 변경 되 면 정보를 확인 합니다. **만들기**를 선택 하 여 요청을 제출 합니다. 요청을 수정 하려면 **이전** 을 선택 하 고 단계를 반복 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 전송](../media/setup-direct-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료 될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하세요. 성공적으로 배포 되 면 다음과 같이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 성공](../media/setup-direct-success.png)
