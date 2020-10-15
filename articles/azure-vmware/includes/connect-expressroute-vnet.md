---
title: 가상 네트워크 게이트웨이에 ExpressRoute 연결
description: ExpressRoute를 가상 네트워크 게이트웨이에 연결하는 단계입니다.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 214ef9c01193b238c8e456ef2809f7a2edbdb6c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91598175"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. 이전 자습서에서 만든 프라이빗 클라우드로 이동하여 **관리** 아래에서 **연결**을 선택하고, **ExpressRoute** 탭을 선택합니다.

1. 인증 키를 복사합니다. 권한 부여 키가 없는 경우 **+ 권한 부여 키 요청**을 선택하여 새로 만들어야 합니다.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="권한 부여 키를 복사합니다. 권한 부여 키가 없는 경우 + 권한 부여 키 요청을 선택하여 새로 만들어야 합니다." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

1. 이전 단계에서 만든 가상 네트워크 게이트웨이로 이동하여 **설정** 아래에서 **연결**을 선택합니다. **연결** 페이지에서 **+ 추가**를 선택합니다.

1. **연결 추가** 페이지에서 필드 값을 제공하고 **확인**을 선택합니다. 

   | 필드 | 값 |
   | --- | --- |
   | **이름**  | 연결의 이름을 입력합니다.  |
   | **연결 형식**  | **ExpressRoute**를 선택합니다.  |
   | **인증 사용**  | 이 확인란이 선택되어 있는지 확인합니다.  |
   | **가상 네트워크 게이트웨이** | 이전에 만든 Virtual Network 게이트웨이입니다.  |
   | **인증 키**  | 리소스 그룹에 대한 ExpressRoute 탭에서 인증 키를 복사하여 붙여넣습니다. |
   | **피어 회로 URI**  | 리소스 그룹에 대한 ExpressRoute 탭에서 ExpressRoute ID를 복사하여 붙여넣습니다.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="권한 부여 키를 복사합니다. 권한 부여 키가 없는 경우 + 권한 부여 키 요청을 선택하여 새로 만들어야 합니다." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

ExpressRoute 회로와 Virtual Network 간의 연결이 만들어집니다.