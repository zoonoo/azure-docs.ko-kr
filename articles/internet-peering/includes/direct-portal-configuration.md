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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81681048"
---
1. **피어링 만들기** 페이지의 **구성** 탭에서 다음과 같이 입력란을 채웁니다.

    * **피어링 유형** 으로 **Direct(직접)** 를 선택합니다.
    * **Microsoft 네트워크** 의 경우 **AS8075** 를 선택합니다. ASN 8069와의 피어링을 만들지 마세요. ASN 8069는 특수 애플리케이션용으로 예약되어 있으며 [Microsoft 피어링](mailto:peering@microsoft.com)에서만 사용됩니다.
    * **기본 무료** 로 **SKU** 를 선택합니다. 프리미엄 무료는 특수 애플리케이션용으로 예약되어 있으므로 선택하지 마세요.
    * 피어링을 설정하려는 **메트로** 위치를 선택합니다.

        > [!NOTE]
        > 선택한 **Metro(메트로)** 위치에서 Microsoft와의 피어링 연결이 이미 있고 Azure Portal을 처음 사용하여 해당 위치에서 피어링을 설정하는 경우 기존 피어링 연결이 표시된 대로 **피어링 연결** 섹션에 나열됩니다. 새 연결과 함께 모든 연결을 한 곳에서 관리할 수 있도록 Microsoft는 이러한 피어링 연결을 Azure 리소스로 자동 변환합니다. 자세한 내용은 [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](../howto-legacy-direct-portal.md)을 참조하세요.
        >

1. **피어링 연결** 에서 **새로 만들기** 를 선택하여 설정하려는 각 새 연결에 대한 줄을 추가합니다.

    * 연결 설정을 구성하거나 수정하려면 줄에 대한 편집 단추를 선택합니다.

        > [!div class="mx-imgBorder"]
        > ![편집 단추](../media/setup-direct-conf-tab-edit.png)
    
    * 줄을 삭제하려면 **...**  > **삭제** 를 선택합니다.

        > [!div class="mx-imgBorder"]
        > ![삭제 단추](../media/setup-direct-conf-tab-delete.png)

    * 여기에 표시된 것처럼 연결에 대한 모든 설정을 제공해야 합니다.

         > [!div class="mx-imgBorder"]
         > ![직접 피어링 연결 페이지](../media/setup-direct-conf-tab-connection.png)

        1. 연결을 설정해야 하는 **피어링 시설** 을 선택합니다.
        1. **세션 주소 공급자** 는 네트워크와 Microsoft 간 BGP 세션을 설정하는 데 필요한 서브넷의 공급자를 결정하는 데 사용됩니다. 서브넷을 제공할 수 있는 경우 **피어** 를 선택합니다. 그렇지 않은 경우 **Microsoft** 를 선택하면 [Microsoft 피어링](mailto:peering@microsoft.com)이 연락을 드립니다. 이 옵션을 선택하면 Microsoft에서 피어링 요청을 처리하는 데 시간이 더 걸립니다. 경우에 따라 Microsoft에서 서브넷을 제공하지 못하여 요청이 거부될 수 있습니다.
        1. **세션 주소 공급자** 옵션을 **피어** 로 선택한 경우 **Session IPv4 prefix(세션 IPv4 접두사)** 및 **Session IPv6 prefix(세션 IPv6 접두사)** 상자에 각각 접두사 마스크와 함께 IPv4 및 IPv6 주소를 입력합니다.
        1. **최대 보급 IPv4 주소** 및 **최대 보급 IPv6 주소** 상자에 각각 보급할 IPv4 및 IPv6 접두사 수를 입력합니다.
        1. **총 대역폭** 슬라이더를 조정하여 연결 대역폭을 반영합니다.
        1. **저장** 을 선택하여 연결 설정을 저장합니다.

1. 이전 단계를 반복하여 이전에 선택한 **Metro(메트로)** 내에서 Microsoft가 네트워크와 함께 배치된 시설에서 연결을 더 추가합니다.

1. 필요한 모든 연결을 추가한 후 **검토 + 만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 구성 탭 최종](../media/setup-direct-conf-tab-final.png)

1. 포털에서 입력한 정보의 기본 유효성 검사를 실행합니다. 맨 위에 있는 리본에는 *최종 유효성 검사 실행 중...* 이라는 메시지가 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 유효성 검사 탭](../media/setup-direct-review-tab-validation.png)

1. 메시지가 *유효성 검사 통과* 로 변경되면 정보를 확인합니다. **만들기** 를 선택하여 요청을 제출합니다. 요청을 수정하려면 **이전** 을 선택하고 단계를 반복합니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 제출](../media/setup-direct-review-tab-submit.png)

1. 요청을 제출한 후 배포가 완료될 때까지 기다립니다. 배포에 실패하면 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하세요. 성공적으로 배포되면 다음과 같이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![피어링 성공](../media/setup-direct-success.png)
