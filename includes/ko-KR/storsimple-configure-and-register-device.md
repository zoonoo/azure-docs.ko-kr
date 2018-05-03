<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>장치를 구성 및 등록하려면
1. StorSimple 장치 직렬 콘솔에서 Windows PowerShell 인터페이스에 액세스합니다. 지침은 [장치 직렬 콘솔 연결에 PuTTY 사용](#use-putty-to-connect-to-the-device-serial-console) 을 참조하세요. **과정을 정확하게 따르지 않으면 콘솔에 액세스할 수 없습니다.**
2. 열린 세션에서 Enter를 한 번 눌러 명령 프롬프트를 엽니다. 
3. 장치에 설정하려는 언어를 선택하라는 메시지가 표시됩니다. 언어를 지정하고 Enter를 누릅니다. 
   
    ![StorSimple 구성 및 등록 장치 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. 표시되는 직렬 콘솔 메뉴에서 모든 권한으로 로그온할 수 있는 옵션 1을 선택합니다. 
   
    ![StorSimple 등록 장치 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     5~12단계를 완료하여 장치에 필요한 최소 네트워크 설정을 구성합니다. **이러한 구성 단계는 장치의 활성 컨트롤러에서 수행해야 합니다.** 직렬 콘솔 메뉴는 배너 메시지에 컨트롤러 상태를 나타냅니다. 활성 컨트롤러에 연결되지 않은 경우 연결을 끊고 활성 컨트롤러에 연결합니다.
5. 명령 프롬프트에 암호를 입력합니다. 기본 장치 암호는 **Password1**입니다.
6. 다음 명령을 입력합니다.
   
     `Invoke-HcsSetupWizard` 
7. 장치에 대한 네트워크 설정 구성을 도와주는 설치 마법사가 나타납니다. 다음 정보를 지정합니다. 
   
   * 데이터 0 네트워크 인터페이스의 IP 주소
   * 서브넷 마스크
   * 게이트웨이
   * 주 DNS 서버의 IP 주소
   * 주 NTP 서버의 IP 주소
     
     > [!NOTE]
     > 서브넷 마스크 및 DNS 설정을 적용하려면 몇 분간 대기할 수 있습니다. "장치가 준비되지 않았습니다." 오류 메시지를 받게 되면 활성 컨트롤러의 데이터 0 네트워크 인터페이스에서 실제 네트워크 연결을 확인합니다.
     > 
     > 
8. (선택 사항) 웹 프록시 서버를 구성합니다. 웹 프록시 구성은 선택 사항이지만 **웹 프록시를 사용하면 여기서만 구성할 수 있습니다**. 자세한 내용은 [장치에 웹 프록시 구성](../articles/storsimple/storsimple-configure-web-proxy.md)으로 이동합니다. 이 단계에서 문제가 발생하면 [웹 프록시 구성 중 오류](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings)에 대한 문제 해결 지침을 참조하세요.

     > [!NOTE]
     > Ctrl + C를 눌러 언제든지 설치 마법사를 종료할 수 있습니다. 이 명령을 실행하기 전에 적용된 모든 설정은 유지됩니다.

1. 보안상의 이유로 첫 번째 세션이 끝난 후 장치 관리자 암호가 만료되고 후속 세션을 위해 암호를 변경해야 합니다. 메시지가 표시되면 장치 관리자 암호를 제공합니다. 유효한 장치 관리자 암호는 8자에서 15자 사이여야 합니다. 암호는 소문자, 대문자, 숫자 및 특수 문자의 조합이어야 합니다.
2. StorSimple 스냅숏 관리자 암호도 여기서 설정합니다. StorSimple 스냅숏 관리자를 실행하는 Windows 호스트로 장치를 인증하는 경우 이 암호를 사용합니다. 메시지가 표시되면 14~15자의 암호를 입력합니다. 암호는 다음 중 세 가지의 조합이어야 함: 소문자, 대문자, 숫자 및 특수 문자. 
   
   ![StorSimple 등록 장치 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   StorSimple  관리자 서비스 인터페이스에서 StorSimple 스냅숏 관리자 암호를 재설정할 수 있습니다. 자세한 단계를 보려면 [StorSimple Manager 서비스를 사용하여 StorSimple 암호 변경](../articles/storsimple/storsimple-change-passwords.md)으로 이동하세요.
   
   이 단계에서 발생하는 문제를 해결하려면 [암호와 관련된 오류](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords)에 대한 문제 해결 지침을 참조하세요.
3. 설치 마법사의 마지막 단계에서는 StorSimple 관리자 서비스에 장치를 등록합니다. 이 경우 2단계에서 얻은 서비스 등록 키가 필요합니다. 등록 키를 입력한 후 장치가 등록되려면 2~3분 정도 기다려야 할 수 있습니다.
   
   가능한 모든 장치 등록 오류를 해결하려면 [장치 등록 중 오류](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration)를 참조하세요. 자세한 문제 해결을 위해 [단계별 문제 해결 예제](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example)를 참조할 수도 있습니다.
4. 장치를 등록한 후 서비스 데이터 암호화 키가 표시됩니다. 이 키를 복사하고 안전한 위치에 저장합니다.
   
   > [!WARNING]
   > 이 키는 StorSimple 관리자 서비스로 추가 장치를 등록하기 위한 서비스 등록 키에 필요합니다. 이 키에 대한 자세한 내용은 [StorSimple 보안](../articles/storsimple/storsimple-security.md) 을 참조하세요.
   > 
   > 
   
    ![StorSimple 등록 장치 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    직렬 콘솔 창에서 텍스트를 복사하려면 해당 텍스트를 선택하면 됩니다. 그런 다음 클립보드 또는 임의의 텍스트 편집기에 붙여넣을 수 있습니다. Ctrl + C를 사용하여 서비스 데이터 암호화 키를 복사하지 마세요. Ctrl + C를 사용하면 설치 마법사가 종료됩니다. 결과적으로, 장치 관리자 암호 및 StorSimple 스냅숏 관리자 암호는 변경되지 않으며 장치는 기본 암호로 되돌아갑니다.
5. 직렬 콘솔을 종료합니다.
6. Azure 클래식 포털로 돌아가 다음 단계를 완료합니다.
   
   1. StorSimple 관리자 서비스를 두 번 클릭하여 **퀵 스타트** 페이지에 액세스합니다.
   2. **연결된 장치 보기**를 클릭합니다.
   3. **장치** 페이지에서 상태를 조회하여 장치가 서비스에 성공적으로 연결되었는지 확인합니다. 장치 상태는 **온라인**이어야 합니다. 장치 상태가 **오프라인**이면 장치가 온라인 상태가 될 때까지 몇 분 정도 기다립니다.
   
   ![StorSimple 장치 페이지](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > 장치가 온라인 상태로 전환되면 이 단계의 시작 부분에서 분리한 네트워크 케이블을 연결합니다.
   > 
   > 

장치가 성공적으로 등록되고 온라인 상태로 전환되지 않으면 `Test-HcsmConnection -Verbose` 를 실행하여 네트워크 연결이 정상인지 확인할 수 있습니다. 이 cmdlet의 자세한 사용법을 보려면 [Test-HcsmConnection에 대한 cmdlet 참조](https://technet.microsoft.com/library/dn715782.aspx)로 이동하세요.

![동영상 사용 가능](./media/storsimple-configure-and-register-device/Video_icon.png) **동영상 사용 가능**

StorSimple용 Windows PowerShell을 통해 장치를 구성 및 등록하는 방법을 보여 주는 동영상을 시청하려면 [여기](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/)를 클릭하세요.

