---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e4921be3116754f146ed0845513010f2642c97b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165603"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 유지 관리 모드 핫픽스를 설치하려면
> [!IMPORTANT]
> 유지 관리 모드에서는 핫픽스를 각 컨트롤러에 차례대로 적용해야 합니다.
> 
> 

1. 디바이스를 유지 관리 모드로 설정합니다. 유지 관리 모드로 전환하는 방법에 대한 지침은 [2단계: 유지 관리 모드 전환](../articles/storsimple/storsimple-update-device.md#step2) 을 참조하세요.
2. 핫픽스를 적용하려면 다음을 입력합니다.
   
     `Start-HcsHotfix` 
3. 메시지가 표시되면 핫픽스 파일이 포함된 네트워크 공유 폴더의 경로를 입력합니다.
4. 확인하라는 메시지가 표시됩니다. **Y** 를 입력하여 핫픽스 설치를 진행합니다.
5. 컨트롤러 하나에 핫픽스를 적용한 후 다른 컨트롤러에 로그인합니다. 이전 컨트롤러에서와 같이 핫픽스를 적용합니다.
6. 핫픽스를 적용한 후 유지 관리 모드를 종료합니다. 지침은 [4단계: 유지 관리 모드 종료](../articles/storsimple/storsimple-update-device.md#step4) 를 참조하세요.

