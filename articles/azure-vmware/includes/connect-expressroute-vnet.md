---
title: 가상 네트워크 게이트웨이에 ExpressRoute 연결
description: ExpressRoute를 가상 네트워크 게이트웨이에 연결하는 단계입니다.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 115e8829723c25fb1644f3f5652fbace529a05cb
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945690"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->


1. ExpressRoute 권한 부여 키를 요청합니다.

   [!INCLUDE [request-authorization-key](request-authorization-key.md)]

1. 사용하려는 가상 네트워크 게이트웨이로 이동하고 **연결** >  **+ 추가** 를 선택합니다.

1. **연결 추가** 페이지에서 필드 값을 제공하고 **확인** 을 선택합니다. 

   | 필드 | 값 |
   | --- | --- |
   | **이름**  | 연결의 이름을 입력합니다.  |
   | **연결 형식**  | **ExpressRoute** 를 선택합니다.  |
   | **인증 사용**  | 이 확인란이 선택되어 있는지 확인합니다.  |
   | **가상 네트워크 게이트웨이** | 사용하려는 가상 네트워크 게이트웨이입니다.  |
   | **인증 키**  | 이전에 복사한 권한 부여 키를 붙여 넣습니다. |
   | **피어 회로 URI**  | 이전에 복사한 ExpressRoute ID를 붙여 넣습니다.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="가상 네트워크 게이트웨이에 ExpressRoute를 연결하기 위한 연결 추가 페이지의 스크린샷.":::

ExpressRoute 회로와 Virtual Network 간의 연결이 만들어집니다.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="가상 네트워크 게이트웨이 연결의 스크린샷.":::