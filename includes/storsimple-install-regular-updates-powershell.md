---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171871"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 정기적인 업데이트를 설치하려면
1. 디바이스 직렬 콘솔을 열고 옵션 1, **모든 권한으로 로그인**을 선택합니다. 암호를 입력합니다. 기본 암호는 *Password1*입니다. 
2. 명령 프롬프트에서 다음을 입력합니다.
   
     `Get-HcsUpdateAvailability`
   
    업데이트가 사용 가능한지 여부 및 업데이트 시 장치를 중단해야 하는지 여부에 대한 알림이 표시됩니다.
3. 명령 프롬프트에서 다음을 입력합니다.
   
     `Start-HcsUpdate`
   
    업데이트 프로세스가 시작됩니다.

> [!IMPORTANT]
> * 이 명령은 정기적인 업데이트에만 적용됩니다. 컨트롤러 하나에서만 이 명령을 실행하면 두 컨트롤러가 모두 업데이트됩니다. 
> * 업데이트 프로세스 중에 컨트롤러 장애 조치(failover)가 수행되지만 시스템 가용성이나 작업에는 영향이 없습니다.
> 
> 

