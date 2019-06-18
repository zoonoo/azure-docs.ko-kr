---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171956"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 정기적인 핫픽스를 설치하려면
1. 디바이스 직렬 콘솔에 연결합니다. 자세한 내용은 [1단계: 직렬 콘솔에 연결](../articles/storsimple/storsimple-update-device.md#step1)을 참조하세요.
2. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다. 암호를 입력합니다. 기본 암호는 **Password1**입니다.
3. 명령 프롬프트에서 다음을 입력합니다.
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > 이 명령은 정기적인 핫픽스에만 적용됩니다. 컨트롤러 하나에서만 이 명령을 실행하면 두 컨트롤러가 모두 업데이트됩니다.
    > 업데이트 프로세스 중에 컨트롤러 장애 조치(failover)가 수행되지만 시스템 가용성이나 작업에는 영향이 없습니다.

4. 메시지가 표시되면 핫픽스 파일이 포함된 네트워크 공유 폴더의 경로를 입력합니다.
5. 확인하라는 메시지가 표시됩니다. **Y** 를 입력하여 핫픽스 설치를 진행합니다.

