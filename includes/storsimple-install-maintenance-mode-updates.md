<properties
   pageTitle="유지 관리 모드 업데이트 설치"
   description="StorSimple용 Windows PowerShell을 사용하여 유지 관리 모드 업데이트를 설치하는 방법을 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/21/2015"
   ms.author="v-sharos" />

### StorSimple용 Windows PowerShell을 통해 유지 관리 모드 업데이트를 설치하려면

1. 장치 직렬 콘솔에 액세스한 다음\(아직 액세스하지 않은 경우\) 옵션 1, **모든 권한으로 로그인**을 선택합니다. 

2. 암호를 입력합니다. 기본 암호는 **Password1**입니다.

3. 명령 프롬프트에 다음을 입력합니다.

   **Get-HcsUpdateAvailability**
    
    You will be notified if updates are available and whether the updates are disruptive or non-disruptive.

4. 장치를 중단하는 업데이트를 적용하려면 장치를 유지 관리 모드로 설정해야 합니다. 지침은 [유지 관리 모드를 설정하려면](#to-enter-maintenance-mode)을 참조하세요.

5. 장치가 유지 관리 모드 상태일 때 명령 프롬프트에 다음을 입력합니다.

    **Start-HcsUpdate**

6. 확인하라는 메시지가 표시됩니다. 업데이트를 확인하고 나면 현재 액세스 중인 컨트롤러에 업데이트가 설치됩니다. 업데이트가 설치되고 나면 컨트롤러가 다시 시작됩니다.

7. 첫 번째 컨트롤러의 다시 시작이 완료되면 다른 컨트롤러에 연결한 후 1\~6단계를 수행합니다.

8. 두 컨트롤러를 모두 업데이트한 후 유지 관리 모드를 종료합니다. 지침은 [유지 관리 모드를 종료하려면]\](#to-exit-maintenance-mode)을 참조하세요.

<!--HONumber=52-->
