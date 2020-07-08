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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680923"
---
1. **피어 링 만들기** 페이지의 **구성** 탭에서 표시 된 대로 상자를 입력 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 페이지 교환 피어 링 유형 만들기](../media/setup-exchange-conf-tab.png)

    * **피어 링 유형**으로 **Exchange**를 선택 합니다.
    * **기본 무료**로 **SKU** 를 선택 합니다.
    * 피어 링을 설정 하려는 **Metro** 위치를 선택 합니다.

        > [!NOTE]
        > 선택한 **Metro** 위치에서 Microsoft와 이미 연결 된 피어 링을 사용 하 고 해당 위치에서 피어 링을 설정 하는 데 처음으로 포털을 사용 하는 경우 기존 피어 링 연결이 표시 된 것 처럼 **피어 링 연결** 섹션에 나열 됩니다. Microsoft는 이러한 피어 링 연결을 Azure 리소스로 자동 변환 하 여 한 곳에서 새 연결을 함께 관리할 수 있도록 합니다. 자세한 내용은 [포털을 사용 하 여 레거시 Exchange 피어 링을 Azure 리소스로 변환](../howto-legacy-exchange-portal.md)을 참조 하세요.
        >

1. **피어 링 연결**에서 **새로 만들기** 를 선택 하 여 설정 하려는 각 새 연결에 대 한 줄을 추가 합니다.

    * 연결 설정을 구성 하거나 수정 하려면 줄에 대 한 편집 단추를 선택 합니다.

        > [!div class="mx-imgBorder"]
        > ![편집 단추](../media/setup-exchange-conf-tab-edit.png)

    * 줄을 삭제 **하려면 ...를 선택**  >  합니다. **삭제**.

        > [!div class="mx-imgBorder"]
        > ![삭제 단추](../media/setup-exchange-conf-tab-delete.png)

    * 여기에 표시 된 것 처럼 연결에 대 한 모든 설정을 제공 해야 합니다.

         > [!div class="mx-imgBorder"]
         > ![Exchange 피어 링 연결 페이지](../media/setup-exchange-conf-tab-connection.png)

        1. 연결을 설정 해야 하는 **피어 링 기능** 을 선택 합니다.
        1. **Ipv4 주소** 및 **ipv6 주소** 상자에 인접 명령을 사용 하 여 Microsoft 라우터에서 구성 된 ipv4 및 ipv6 주소를 각각 입력 합니다.
        1. 각각의 **보급 된 ipv4 주소** 및 **최대 보급 ipv6 주소** 상자에 보급할 ipv4 및 ipv6 접두사의 수를 입력 합니다.
        1. **확인** 을 선택 하 여 연결 설정을 저장 합니다.

1. 이전에 선택한 **Metro** 내에서 Microsoft가 네트워크와 공동 배치 하는 모든 시설에서 더 많은 연결을 추가 하는 단계를 반복 합니다.

1. 필요한 모든 연결을 추가한 후 **검토 + 만들기**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 구성 탭](../media/setup-exchange-conf-tab-final.png)

1. 포털에서 입력 한 정보에 대 한 기본 유효성 검사를 실행 합니다. 맨 위에 있는 리본은 *최종 유효성 검사를 실행*하는 메시지를 표시 합니다 ....

    > [!div class="mx-imgBorder"]
    > ![피어 링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사 성공*으로 변경 되 면 정보를 확인 합니다. **만들기**를 선택 하 여 요청을 제출 합니다. 요청을 수정 하려면 **이전** 을 선택 하 고 단계를 반복 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 전송](../media/setup-exchange-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료 될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하세요. 성공적으로 배포 되 면 다음과 같이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 성공](../media/setup-direct-success.png)
