---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73491412"
---
1. 생성한 Virtual WAN을 찾습니다. Virtual WAN 페이지의 **연결** 섹션에서 **허브**를 선택합니다.
2. 허브 페이지에서 **+새 허브**를 선택하여 **가상 허브 만들기** 페이지를 엽니다.
3. **가상 허브 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 완료합니다.

   ![기본 사항](./media/virtual-wan-tutorial-er-hub-include/hub1.png "기본 사항")

    **프로젝트 세부 정보**

   * 지역(이전에는 위치라고 했음)
   * 속성
   * 허브 프라이빗 주소 공간. 허브를 만들기 위한 최소 주소 공간은 /24이며 이것은 /25부터 /32까지의 모든 범위가 생성 중에 오류가 발생함을 의미합니다.
4. **Express 경로 탭**을 선택합니다.

5. **Express 경로** 탭에서 다음 필드를 완료합니다.

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * **예**를 선택하여 **Express 경로** 게이트웨이를 만듭니다.
   * 드롭다운에서 **게이트웨이 배율 단위** 값을 선택합니다.
6. **검토 + 만들기**를 선택하여 유효한지 확인합니다.
7. **만들기**를 선택하여 허브를 만듭니다. 30분 후에 **새로 고침**을 선택하여 **허브** 페이지에서 허브를 봅니다. **리소스로 이동**을 선택하여 리소스로 이동합니다.