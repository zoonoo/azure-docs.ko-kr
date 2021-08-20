---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/15/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a1abf76ee8e6c44eaa61d9de38b163f230327edd
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362313"
---
1. Virtual WAN 페이지의 왼쪽 창에서 **허브** 를 선택합니다. **허브** 페이지에서 **+새 허브** 를 선택합니다.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.png" alt-text="새 허브의 스크린샷.":::

1. **가상 허브 만들기** 페이지에서 다음 필드를 완료합니다.

   * **영역** - 가상 허브를 배포할 영역을 선택합니다.
   * **이름** - 가상 허브를 호출할 이름을 입력합니다.
   * **허브 프라이빗 주소 공간** - CIDR 표기법으로 된 허브의 주소 범위입니다.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.png" alt-text="가상 허브 만들기의 스크린샷.":::

1. **지점 및 사이트 간** 탭에서 다음 필드를 완료합니다.

   * **게이트웨이 배율 단위** - 사용자 VPN 게이트웨이의 집계 용량을 나타냅니다.
   * **지점 및 사이트 간 구성** - 이전 단계에서 만든 것입니다.
   * **클라이언트 주소 풀** - 원격 사용자용입니다.
   * **사용자 지정 DNS 서버 IP**
   * **라우팅 기본 설정** - 적절한 라우팅 기본 설정을 선택합니다. Azure 라우팅 기본 설정을 사용하면 Azure와 인터넷 간의 트래픽 라우팅 방법을 선택할 수 있습니다. Microsoft 네트워크를 통해 또는 ISP 네트워크(공용 인터넷)를 통해 트래픽을 라우팅하도록 선택할 수 있습니다. 이러한 옵션을 각각 콜드 포테이토 라우팅 및 핫 포테이토 라우팅이라고도 합니다. Virtual WAN의 공용 IP 주소는 선택한 라우팅 옵션을 기반으로 서비스에서 할당합니다. Microsoft 네트워크 또는 ISP를 통한 라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정](../articles/virtual-network/routing-preference-overview.md) 문서를 참조하세요.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-point-to-site.png" alt-text="지점 및 사이트 간이 선택된 가상 허브 구성의 스크린샷.":::

1. **검토 + 만들기** 를 선택합니다.
1. **유효성 검사 통과** 페이지에서 **만들기** 를 선택합니다.