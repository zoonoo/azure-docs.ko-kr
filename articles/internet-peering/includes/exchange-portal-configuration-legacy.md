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
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775213"
---
1. **피어 링 만들기** 페이지의 **구성** 탭에서 아래와 같이 필드를 입력 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 구성-Exchange](../media/setup-exchange-conf-tab.png)

    * **피어 링 유형**으로 *Exchange*를 선택 합니다.
    * *기본 무료*로 **SKU** 를 선택 합니다.
    * 피어 링을 Azure 리소스로 변환 하려는 **Metro** 위치를 선택 합니다. Azure 리소스로 변환 되지 않은 선택한 **Metro** 위치에서 Microsoft와 피어 링 연결을 사용 하는 경우 아래와 같이 이러한 연결이 **피어 링 연결** 섹션에 나열 됩니다. 이제 이러한 피어 링 연결을 Azure 리소스로 변환할 수 있습니다.

        > [!div class="mx-imgBorder"]
        > ![피어 링 구성-Exchange-레거시 연결](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > 레거시 피어 링 연결에 대 한 설정은 수정할 수 없습니다. 선택한 **Metro** 위치에 Microsoft와 피어 링 연결을 추가 하려면 **새로 만들기** 단추를 클릭 하면 됩니다. 자세한 정보는 [포털을 사용 하 여 Exchange 피어 링 만들기 또는 수정](../howto-exchange-portal.md) 을 참조 하세요.
        >

1. **검토 + 만들기**를 클릭 합니다. 포털에서 입력 한 정보의 기본 유효성 검사를 실행 하는지 확인 합니다. *최종 유효성 검사를 실행*하는 것 처럼 위쪽의 리본에 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. *유효성 검사를 통과*한 후에는 정보를 확인 하 고 **만들기**를 클릭 하 여 요청을 제출 합니다. 요청을 수정 해야 하는 경우 **이전** 을 클릭 하 고 위의 단계를 반복 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 전송](../media/setup-exchange-review-tab-submit.png)

1. 요청을 제출 하 고 나면 배포를 완료할 때까지 기다립니다. 배포에 실패 하는 경우 [Microsoft 피어 링](mailto:peering@microsoft.com)에 문의 하십시오. 성공적으로 배포 되 면 다음과 같이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 성공](../media/setup-direct-success.png)
