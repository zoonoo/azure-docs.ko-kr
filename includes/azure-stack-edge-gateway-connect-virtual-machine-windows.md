---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101730650"
---
VM을 만드는 동안 전달한 IP를 통해 RDP(원격 데스크톱 프로토콜)를 사용하여 Windows VM에 연결합니다.

1. 클라이언트에서 RDP를 엽니다. 
1. **시작** 으로 이동하여 **mstsc** 를 입력합니다.
1. **원격 데스크톱 연결** 창에서 VM의 IP 주소와 VM 템플릿 매개 변수 파일에서 사용한 액세스 자격 증명을 입력한 다음 **연결** 을 선택합니다.

   ![RDP를 통해 Windows VM에 연결하는 원격 데스크톱 연결 창의 스크린샷](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > 신뢰할 수 없는 컴퓨터에 대한 연결을 승인해야 할 수도 있습니다. 

이제 어플라이언스에서 실행되는 VM에 로그인되었습니다. 
