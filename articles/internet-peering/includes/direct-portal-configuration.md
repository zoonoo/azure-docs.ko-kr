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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81681048"
---
1. **피어 링 만들기** 페이지의 **구성** 탭에서 여기에 표시 된 대로 상자를 입력 합니다.

    * **피어 링 유형**으로 **Direct**를 선택 합니다.
    * **Microsoft 네트워크**에서 **AS8075**를 선택 합니다. ASN 8069를 사용 하 여 피어 링을 만들지 마세요. 특수 응용 프로그램용으로 예약 되어 있으며 [Microsoft 피어 링](mailto:peering@microsoft.com)에서만 사용 됩니다.
    * **기본 무료**로 **SKU** 를 선택 합니다. 특수 응용 프로그램용으로 예약 되어 있으므로 프리미엄 무료를 선택 하지 마세요.
    * 피어 링을 설정 하려는 **Metro** 위치를 선택 합니다.

        > [!NOTE]
        > 선택한 **Metro** 위치에서 Microsoft와 이미 연결 된 피어 링을 사용 하 고 해당 위치에서 피어 링을 설정 하는 데 처음으로 Azure Portal를 사용 하는 경우에는 표시 된 대로 **피어 링 연결** 섹션에 기존 피어 링 연결이 나열 됩니다. Microsoft는 이러한 피어 링 연결을 Azure 리소스로 자동 변환 하 여 새 연결을 모두 한 곳에서 관리할 수 있도록 합니다. 자세한 내용은 [포털을 사용 하 여 레거시 직접 피어 링을 Azure 리소스로 변환](../howto-legacy-direct-portal.md)을 참조 하세요.
        >

1. **피어 링 연결**에서 **새로 만들기** 를 선택 하 여 설정 하려는 각 새 연결에 대 한 줄을 추가 합니다.

    * 연결 설정을 구성 하거나 수정 하려면 줄에 대 한 편집 단추를 선택 합니다.

        > [!div class="mx-imgBorder"]
        > ![편집 단추](../media/setup-direct-conf-tab-edit.png)
    
    * 줄을 삭제 **하려면 ...를 선택**  >  합니다. **삭제**.

        > [!div class="mx-imgBorder"]
        > ![삭제 단추](../media/setup-direct-conf-tab-delete.png)

    * 여기에 표시 된 것 처럼 연결에 대 한 모든 설정을 제공 해야 합니다.

         > [!div class="mx-imgBorder"]
         > ![직접 피어 링 연결 페이지](../media/setup-direct-conf-tab-connection.png)

        1. 연결을 설정 해야 하는 **피어 링 기능** 을 선택 합니다.
        1. **세션 주소 공급자** 는 네트워크와 MICROSOFT 간의 BGP 세션을 설정 하는 데 필요한 서브넷을 제공 하는 사용자를 결정 하는 데 사용 됩니다. 서브넷을 제공할 수 있는 경우 **피어**를 선택 합니다. 그렇지 않은 경우 **microsoft** 및 [microsoft 피어 링](mailto:peering@microsoft.com) 을 선택 하면 연락을 드립니다. 이 옵션을 선택 하면 Microsoft에서 피어 링 요청을 처리 하는 데 더 오랜 시간이 걸립니다. 경우에 따라 Microsoft에서 서브넷을 제공 하지 못할 수 있으며,이로 인해 요청이 거부 됩니다.
        1. **세션 주소 공급자** 옵션을 **피어**로 선택한 경우에는 ipv4 및 IPv6 주소를 **세션 ipv4 접두사** 및 **세션 IPv6 접두사** 상자에 각각 접두사 마스크와 함께 입력 합니다.
        1. 각각의 **보급 된 ipv4 주소** 및 **최대 보급 ipv6 주소** 상자에 보급할 ipv4 및 ipv6 접두사의 수를 입력 합니다.
        1. **총 대역폭** 슬라이더를 조정 하 여 연결 대역폭을 반영 합니다.
        1. **저장** 을 선택 하 여 연결 설정을 저장 합니다.

1. 이전 단계를 반복 하 여 Microsoft가 이전에 선택한 **Metro** 내에서 네트워크와 공동 배치 하는 모든 시설에서 더 많은 연결을 추가 합니다.

1. 필요한 모든 연결을 추가한 후 **검토 + 만들기**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 구성 탭 최종](../media/setup-direct-conf-tab-final.png)

1. 포털에서 입력 한 정보에 대 한 기본 유효성 검사를 실행 합니다. 맨 위에 있는 리본은 *최종 유효성 검사를 실행*하는 메시지를 표시 합니다 ....

    > [!div class="mx-imgBorder"]
    > ![피어 링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사 성공*으로 변경 되 면 정보를 확인 합니다. **만들기**를 선택 하 여 요청을 제출 합니다. 요청을 수정 하려면 **이전** 을 선택 하 고 단계를 반복 합니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 전송](../media/setup-direct-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료 될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하세요. 성공적으로 배포 되 면 다음과 같이 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어 링 성공](../media/setup-direct-success.png)
