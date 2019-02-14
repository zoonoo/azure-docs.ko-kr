---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889694"
---
#### <a name="to-enter-maintenance-mode"></a>유지 관리 모드를 설정하려면
1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다.
2. 암호를 입력합니다. 기본 암호는 **Password1**입니다.
3. 명령 프롬프트에 다음을 입력합니다.
   
     `Enter-HcsMaintenanceMode`
4. 유지 관리 모드에서는 모든 I/O 요청이 중단되며 Azure 클래식 포털에 대한 연결이 끊어짐을 알리는 경고 메시지와 확인 요청 메시지가 표시됩니다. **Y** 를 입력하여 유지 관리 모드를 설정합니다.
   
    두 컨트롤러가 모두 다시 시작됩니다. 다시 시작이 완료되면 디바이스가 유지 관리 모드임을 나타내는 다른 메시지가 표시됩니다.

