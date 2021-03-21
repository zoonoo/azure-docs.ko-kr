---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730650"
---
VM을 만드는 동안 전달 된 IP를 통해 원격 데스크톱 프로토콜 (RDP)를 사용 하 여 Windows VM에 연결 합니다.

1. 클라이언트에서 RDP를 엽니다. 
1. **시작** 으로 이동 하 여 **mstsc** 를 입력 합니다.
1. **원격 데스크톱 연결** 창에서 VM의 IP 주소와 vm 템플릿 매개 변수 파일에서 사용한 액세스 자격 증명을 입력 한 다음 **연결** 을 선택 합니다.

   ![RDP를 통해 Windows VM에 연결 하는 원격 데스크톱 연결 창의 스크린샷](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > 신뢰할 수 없는 컴퓨터에 대 한 연결을 승인 해야 할 수도 있습니다. 

이제 어플라이언스에서 실행 되는 VM에 로그인 합니다. 
