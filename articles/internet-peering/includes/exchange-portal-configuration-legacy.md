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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678521"
---
1. **피어 링 만들기** 페이지의 **구성** 탭에서 여기에 표시 된 대로 상자를 입력 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 페이지 교환 피어 링 유형 만들기](../media/setup-exchange-conf-tab.png)

    * **피어 링 유형**으로 **Exchange**를 선택 합니다.
    * **기본 무료**로 **SKU** 를 선택 합니다.
    * 피어 링을 Azure 리소스로 변환 하려는 **Metro** 위치를 선택 합니다. Azure 리소스로 변환 되지 않은 선택한 **Metro** 위치에서 Microsoft와 피어 링 연결을 사용 하는 경우 이러한 연결은 다음과 같이 **피어 링 연결** 섹션에 나열 됩니다. 이제 이러한 피어 링 연결을 Azure 리소스로 변환할 수 있습니다.

        > [!div class="mx-imgBorder"]
        > ![피어 링 연결 목록](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > 레거시 피어 링 연결에 대 한 설정은 수정할 수 없습니다. 선택한 **Metro** 위치에 Microsoft와 피어 링 연결을 추가 하려면 **새로 만들기**를 선택 합니다. 자세한 내용은 [포털을 사용 하 여 Exchange 피어 링 만들기 또는 수정](../howto-exchange-portal.md)을 참조 하세요.
        >

1. **검토 + 만들기**를 선택합니다. 포털에서 입력 한 정보에 대 한 기본 유효성 검사를 실행 합니다. 맨 위에 있는 리본은 *최종 유효성 검사를 실행*하는 메시지를 표시 합니다 ....

    > [!div class="mx-imgBorder"]
    > ![피어 링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사 성공*으로 변경 되 면 정보를 확인 합니다. **만들기**를 선택 하 여 요청을 제출 합니다. 요청을 수정 해야 하는 경우 **이전** 을 선택 하 고 단계를 반복 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 전송](../media/setup-exchange-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료 될 때까지 기다립니다. 배포에 실패 하는 경우 [Microsoft 피어 링](mailto:peering@microsoft.com)에 문의 하십시오. 성공적으로 배포 되 면 다음과 같이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 성공](../media/setup-direct-success.png)
