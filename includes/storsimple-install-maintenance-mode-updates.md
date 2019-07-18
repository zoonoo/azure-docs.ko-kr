---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182238"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 유지 관리 모드 업데이트를 설치하려면
1. 디바이스 직렬 콘솔에 액세스한 다음(아직 액세스하지 않은 경우) 옵션 1, **모든 권한으로 로그인**을 선택합니다. 
2. 암호를 입력합니다. 기본 암호는 **Password1**입니다.
3. 명령 프롬프트에서 다음을 입력합니다.
   
     `Get-HcsUpdateAvailability` 
4. 업데이트가 사용 가능한지 여부 및 업데이트 시 장치를 중단해야 하는지 여부에 대한 알림이 표시됩니다. 디바이스를 중단하는 업데이트를 적용하려면 디바이스를 유지 관리 모드로 설정해야 합니다. 지침은 [2단계: 유지 관리 모드 종료](../articles/storsimple/storsimple-update-device.md#step2)를 참조하세요.
5. 디바이스가 유지 관리 모드 상태일 때 명령 프롬프트에 다음을 입력합니다. `Start-HcsUpdate`
6. 확인하라는 메시지가 표시됩니다. 업데이트를 확인하고 나면 현재 액세스 중인 컨트롤러에 업데이트가 설치됩니다. 업데이트가 설치되고 나면 컨트롤러가 다시 시작됩니다. 
7. 업데이트 상태를 모니터링합니다. 형식:
   
    `Get-HcsUpdateStatus`
   
    `RunInProgress`가 `True`이면 업데이트가 아직 진행 중입니다. `RunInProgress`가 `False`이면 업데이트가 완료된 것입니다.  
8. 업데이트가 현재 컨트롤러에 설치되고 다시 시작된 경우 다른 컨트롤러에 연결하여 1~6단계를 수행합니다.
9. 두 컨트롤러를 모두 업데이트한 후 유지 관리 모드를 종료합니다. 지침은 [4단계: 유지 관리 모드 종료](../articles/storsimple/storsimple-update-device.md#step4)를 참조하세요.

