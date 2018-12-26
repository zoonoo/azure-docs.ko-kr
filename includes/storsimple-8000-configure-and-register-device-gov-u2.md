---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c54431009105c1d213e6cefeba06c39c781e2c1a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166152"
---
<!--author=SharS last changed: 06/22/2016-->

### <a name="to-configure-and-register-the-device"></a>디바이스를 구성 및 등록하려면
1. StorSimple 디바이스 직렬 콘솔에서 Windows PowerShell 인터페이스에 액세스합니다. 지침은 [디바이스 직렬 콘솔 연결에 PuTTY 사용](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console)을 참조하세요. **과정을 정확하게 따르지 않으면 콘솔에 액세스할 수 없습니다.**
2. 열린 세션에서 **Enter**를 한 번 눌러 명령 프롬프트를 엽니다.
3. 디바이스에 설정하려는 언어를 선택하라는 메시지가 표시됩니다. 언어를 지정하고 **Enter**를 누릅니다.
   
    ![StorSimple 구성 및 등록 디바이스 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. 표시되는 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다.
   
    ![StorSimple 등록 디바이스 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. 다음 단계를 수행하여 디바이스에 필요한 최소 네트워크 설정을 구성합니다.
   
   > [!IMPORTANT]
   > 이러한 구성 단계는 디바이스의 활성 컨트롤러에서 수행해야 합니다. 직렬 콘솔 메뉴는 배너 메시지에 컨트롤러 상태를 나타냅니다. 활성 컨트롤러에 연결되지 않은 경우 연결을 끊은 다음 활성 컨트롤러에 연결합니다.
   
   1. 명령 프롬프트에 암호를 입력합니다. 기본 디바이스 암호는 **Password1**입니다.
   2. 다음 명령을 입력합니다.
      
        `Invoke-HcsSetupWizard`
   3. 디바이스에 대한 네트워크 설정 구성을 도와주는 설치 마법사가 나타납니다. 다음 정보를 지정합니다.
      
      * 데이터 0 네트워크 인터페이스의 IP 주소
      * 서브넷 마스크
      * 게이트웨이
      * 주 DNS 서버의 IP 주소
      * 주 NTP 서버의 IP 주소
      
      > [!NOTE]
      > 서브넷 마스크 및 DNS 설정을 적용하려면 몇 분간 대기해야 할 수 있습니다.
    
   4. 선택적으로 웹 프록시 서버를 구성합니다.
      
      > [!IMPORTANT]
      > 웹 프록시 구성은 선택 사항이지만 웹 프록시를 사용하면 여기서만 구성할 수 있습니다. 자세한 내용은 [디바이스에 웹 프록시 구성](../articles/storsimple/storsimple-configure-web-proxy.md)으로 이동합니다.
     
6. 설치 마법사를 끝내려면 Ctrl + C를 누릅니다.
8. 디바이스가 Microsoft Azure Government 포털을 가리키도록 다음 cmdlet을 실행합니다(기본적으로 공용 Azure 클래식 포털을 가리키기 때문임). 두 컨트롤러가 모두 다시 시작됩니다. 두 PuTTY 세션을 사용하여, 각 컨트롤러가 다시 시작되면 볼 수 있도록 두 컨트롤러에 동시에 연결하는 것이 좋습니다.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   확인 메시지가 표시됩니다. 기본값(**Y**)을 적용합니다.
9. 설치를 다시 시작하려면 다음 cmdlet을 실행합니다.
   
    `Invoke-HcsSetupWizard`
   
    ![설치 마법사 다시 시작](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. 네트워크 설정을 적용합니다. 각 설정에 동의하면 유효성 검사 메시지가 나타납니다.
11. 보안상의 이유로 첫 번째 세션이 끝난 후 디바이스 관리자 암호가 만료되고 지금 암호를 변경해야 합니다. 메시지가 표시되면 디바이스 관리자 암호를 제공합니다. 유효한 디바이스 관리자 암호는 8자에서 15자 사이여야 합니다. 암호는 소문자, 대문자, 숫자 및 특수 문자 중 세 유형을 포함해야 합니다.
    
    <br/>![StorSimple 등록 디바이스 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. 설치 마법사의 마지막 단계에서는 StorSimple 디바이스 관리자 서비스에 디바이스를 등록합니다. 이 경우 [2단계: 서비스 등록 키 가져오기](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key)에서 얻은 서비스 등록 키가 필요합니다. 등록 키를 입력한 후 디바이스가 등록되려면 2~3분 정도 기다려야 할 수 있습니다.
    
    > [!NOTE]
    > Ctrl + C를 눌러 언제든지 설치 마법사를 종료할 수 있습니다. 모든 네트워크 설정(Data 0, 서브넷 마스크 및 게이트웨이 IP 주소)를 입력한 경우, 항목이 유지됩니다.
    
    ![StorSimple 등록 진행률](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. 디바이스를 등록한 후 서비스 데이터 암호화 키가 표시됩니다. 이 키를 복사하고 안전한 위치에 저장합니다. **이 키는 서비스 등록 키와 함께 StorSimple 장치 관리자 서비스에 추가 장치를 등록하는 데 필요합니다.** 이 키에 대한 자세한 내용은 [StorSimple 보안](../articles/storsimple/storsimple-8000-security.md) 을 참조하세요.
    
    ![StorSimple 등록 디바이스 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > 직렬 콘솔 창에서 텍스트를 복사하려면 해당 텍스트를 선택하면 됩니다. 그런 다음 클립보드 또는 임의의 텍스트 편집기에 붙여넣을 수 있습니다.
    > 
    > **Ctrl+C**를 사용하여 서비스 데이터 암호화 키를 복사하지 마세요. **Ctrl+C**를 사용하면 설치 마법사가 종료됩니다. 결과적으로, 디바이스 관리자 암호는 변경되지 않으며 디바이스는 기본 암호로 되돌아갑니다.
    
14. 직렬 콘솔을 종료합니다.
15. Azure Government 포털로 돌아가서 다음 단계를 완료합니다.
    
    1. StorSimple 디바이스 관리자 서비스로 이동합니다.
    2. **장치**를 클릭합니다. 디바이스 목록에서 배포 중인 디바이스를 식별합니다. 상태를 조회하여 디바이스가 서비스에 성공적으로 연결되었는지 확인합니다. 디바이스 상태는 **온라인**이어야 합니다.
            
        디바이스 상태가 **오프라인**이면 디바이스가 온라인 상태가 될 때까지 몇 분 정도 기다립니다.
       
        몇분 후 디바이스가 여전히 오프라인 상태인 경우, [StorSimple 디바이스에 대한 네트워킹 요구 사항](../articles/storsimple/storsimple-8000-system-requirements.md)에 설명된 대로 방화벽 네트워크가 구성되었는지 확인해야 합니다.
       
        9354 포트가 StorSimple 디바이스 관리자 서비스와 디바이스 간 통신용 서비스 버스에 의해 사용될 때 아웃바운드 통신을 위해 열리는지 확인합니다.

