---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812716"
---
1. 가상 WAN 아래에서 허브를 선택하고, **+새 허브**를 선택합니다.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="새 허브":::

1. [가상 허브 만들기] 페이지에서 다음 필드를 채웁니다.

   * **영역** - 가상 허브를 배포할 영역을 선택합니다.
   * **이름** - 가상 허브를 호출할 이름을 입력합니다.
   * **허브 프라이빗 주소 공간** - CIDR 표기법으로 된 허브의 주소 범위입니다.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="새 허브":::

1. 지점 및 사이트 간 탭에서 다음 필드를 완료합니다.

   * **게이트웨이 배율 단위** - 사용자 VPN 게이트웨이의 집계 용량을 나타냅니다.
   * **지점 및 사이트 간 구성** - 이전 단계에서 만든 것입니다.
   * **클라이언트 주소 풀** - 원격 사용자용입니다.
   * **사용자 지정 DNS 서버 IP**

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="새 허브":::

1. **검토 + 만들기**를 선택합니다.
1. **유효성 검사 통과** 페이지에서 **만들기**를 선택합니다.