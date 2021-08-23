---
title: ExpressRoute에 대한 권한 부여 키 요청
description: ExpressRoute에 대한 권한 부여 키를 요청하는 단계.
ms.topic: include
ms.date: 03/15/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 5f4c436a8559a4b579a3a0c9bbfbe79bcb50764e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729139"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Azure Portal에서 Azure VMware Solution 프라이빗 클라우드로 이동합니다. **관리** > **연결** > **ExpressRoute** 를 선택한 다음, **+ 권한 부여 키 요청** 을 선택합니다.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="ExpressRoute 권한 부여 키를 요청하는 방법을 보여주는 스크린샷." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. 이름을 입력하고 **만들기** 를 선택합니다.

   키를 만드는 데 30초 정도 걸릴 수 있습니다. 만들기가 완료되면 프라이빗 클라우드의 권한 부여 키 목록에 새 키가 표시됩니다.

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="ExpressRoute Global Retach 권한 부여 키를 보여주는 스크린샷." lightbox="../media/expressroute-global-reach/show-global-reach-auth-key.png":::
  
1. 권한 부여 키와 ExpressRoute ID를 복사합니다. 피어링을 완료하는 데 필요합니다. 권한 부여 키는 일정 시간 후에 사라지기 때문에 표시되는 즉시 복사합니다.

