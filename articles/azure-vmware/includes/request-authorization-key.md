---
title: ExpressRoute에 대한 권한 부여 키 요청
description: ExpressRoute에 대한 권한 부여 키를 요청하는 단계.
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 54a610c8b0f3f3fe9d3ebe39291bba7767007839
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491849"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Azure Portal의 **연결** 섹션 아래에 있는 **ExpressRoute** 탭에서 **+ 권한 부여 키 요청** 을 선택합니다. 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="ExpressRoute 권한 부여 키를 요청하는 방법을 보여주는 스크린샷." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. 이름을 입력하고 **만들기** 를 선택합니다. 
      
   키를 만드는 데 30초 정도 걸릴 수 있습니다. 만들기가 완료되면 프라이빗 클라우드의 권한 부여 키 목록에 새 키가 표시됩니다.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="ExpressRoute Global Retach 권한 부여 키를 보여주는 스크린샷.":::
  
1. 권한 부여 키와 ExpressRoute ID를 기록해 둡니다. 피어링을 완료하는 데 사용합니다.  

   > [!NOTE]
   > 권한 부여 키는 일정 시간 후에 사라지기 때문에 표시되는 즉시 복사합니다.