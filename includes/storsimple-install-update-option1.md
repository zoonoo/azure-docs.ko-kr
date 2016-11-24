<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-download-hotfixes"></a>핫픽스를 다운로드하려면
소프트웨어 업데이트를 다운로드하려면 다음 단계를 수행합니다.

1. Internet Explorer를 시작하여 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)으로 이동합니다.
2. 이 컴퓨터에서 Microsoft 업데이트 카탈로그를 처음 사용하는 경우 Microsoft 업데이트 카탈로그 추가 기능을 설치하라는 메시지가 나타나면 **설치** 를 클릭합니다.
    ![카탈로그 설치](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)
3. Microsoft 업데이트 카탈로그의 검색 상자에 다운로드하려는 핫픽스의 KB(기술 자료) 번호(예: **3063418**)를 입력하고 **검색**을 클릭합니다.
4. **StorSimple 업데이트 1.2 어플라이언스 업데이트** 번들이 표시됩니다. **추가**를 클릭합니다. 업데이트는 장바구니에 추가됩니다.
5. 위의 표에 나열된 추가적인 핫픽스(**3043005** 및 **3063416**)를 검색하고, 각각 바구니에 추가합니다.
6. **바구니 보기**를 클릭합니다.
   
    ![바구니 보기](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)
7. **다운로드**를 클릭합니다. 다운로드를 표시할 로컬 위치를 지정하거나 **검색** 합니다. 업데이트를 지정된 위치에 다운로드하고 업데이트와 같은 이름의 하위 폴더에 배치합니다. 장치에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.

> [!NOTE]
> 피어 컨트롤러의 잠재적 오류 메시지를 검색하려면 컨트롤러 둘 다에서 핫픽스에 액세스할 수 있어야 합니다.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>일반 모드 핫픽스를 설치 및 확인하려면
일반 모드 핫픽스를 설치 및 확인하려면 다음 단계를 수행합니다. 이미 Azure 포털을 사용하여 설치한 경우 [유지 관리 모드 핫픽스 설치 및 확인](#to-install-and-verify-maintenance-mode-hotfixes)으로 건너뜁니다.

1. 소프트웨어 업데이트를 설치하려면 StorSimple 장치 직렬 콘솔에서 Windows PowerShell 인터페이스에 액세스합니다. [PuTTy를 사용하여 직렬 콘솔에 연결](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)에서 자세한 지침을 따르세요. 명령 프롬프트에서 **Enter**키를 누릅니다.
2. **옵션 1** 을 선택하여 모든 권한으로 장치에 로그온합니다.
3. 업데이트 패키지를 설치하려면 명령 프롬프트에 다음을 입력합니다.
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    위 명령에서 공유 경로에 DNS 대신 IP를 사용합니다. 자격 증명 매개 변수는 인증된 공유에 액세스하는 경우에만 사용됩니다.
   
    자격 증명 매개 변수를 사용하여 공유에 액세스하는 것이 좋습니다. 일반적으로 "모든 사용자"에게 개방된 공유도 인증되지 않은 사용자에게는 개방되지 않습니다.
   
    샘플 출력은 다음과 같습니다.
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```

4. 핫픽스 설치를 확인하라는 메시지가 표시되면 **Y** 를 입력합니다.
5. `Get-HcsUpdateStatus` cmdlet을 사용하여 업데이트를 모니터링합니다.
   
    다음 샘플 출력에서는 진행 중인 업데이트를 보여줍니다. 업데이트가 진행 중이면 `RunInprogress`가 `True`입니다.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp : 9/02/2015 10:36:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     다음 샘플 출력은 업데이트가 완료되었음을 나타냅니다. 업데이트가 완료되면 `RunInProgress`가 `False`입니다.

    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 9/02/2015 10:56:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > 업데이트가 진행 중일 때 cmdlet에서 `False`를 보고하는 경우도 있습니다. 핫픽스가 완료되었는지 확인하려면 몇 분 동안 기다린 후 이 명령을 다시 실행하고 `RunInProgress`가 `False`인지 확인합니다. 맞으면 핫픽스가 완료된 것입니다.
   > 
   > 
6. 소프트웨어 업데이트가 완료된 후 시스템 소프트웨어 버전을 확인합니다. 다음 명령을 입력합니다.
   
    `Get-HcsSystem`
   
    다음 버전이 표시되어야 합니다.
   
   * HcsSoftwareVersion: 6.3.9600.17584
   * CisAgentVersion: 1.0.9049.0
   * MdsAgentVersion: 26.0.4696.1433
     
     업데이트를 적용한 후 버전 번호가 변경되지 않으면 핫픽스를 적용하지 못한 것입니다. 이 경우 추가 지원을 받으려면 [Microsoft 지원](../articles/storsimple/storsimple-contact-microsoft-support.md) 에 문의하세요.
7. 3-5단계를 반복하여 나머지 일반 모드 핫픽스(KB3043005)를 설치합니다.

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>유지 관리 모드 핫픽스를 설치 및 확인하려면
KB3063416를 사용하여 디스크 펌웨어 업데이트를 설치합니다. 작업 중단 업데이트이며 완료하는 데 약 30-45분이 소요됩니다. 장치 직렬 콘솔에 연결하여 계획된 유지 관리 기간에 설치하도록 선택할 수 있습니다.

디스크 펌웨어 업데이트를 설치하려면 아래 지침을 따릅니다.

1. 장치를 유지 관리 모드로 설정합니다. 유지 관리 모드에서 장치에 연결할 때는 Windows PowerShell 원격을 사용해서는 안 됩니다. 장치 직렬 콘솔을 통해 연결된 경우 장치 컨트롤러에서 이 cmdlet을 실행해야 합니다. 형식:
   
    `Enter-HcsMaintenanceMode`
   
    샘플 출력은 다음과 같습니다.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update1-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17584
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    두 컨트롤러 모두 유지 관리 모드로 다시 시작합니다.
2. 디스크 펌웨어 업데이트를 설치하려면 다음을 입력합니다.
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    샘플 출력은 다음과 같습니다.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. `Get-HcsUpdateStatus` 명령을 사용하여 설치 진행률을 모니터링합니다. `RunInProgress`가 `False`로 변경되면 업데이트가 완료됩니다.
4. 설치가 완료된 후에 유지 관리 모드 핫픽스가 설치된 컨트롤러가 다시 부팅됩니다. 모든 권한이 있는 옵션 1로 로그인하고 디스크 펌웨어 버전을 확인합니다. 형식:
   
   `Get-HcsFirmwareVersion`
   
   예상된 디스크 펌웨어 버전은 다음과 같습니다.
   
   `XMGG, XGEE, KZ50, F6C2, VR08`
   
   두 번째 컨트롤러에서 `Get-HcsFirmwareVersion` 명령을 실행하여 해당 소프트웨어 버전이 업데이트되었는지 확인합니다. 그런 다음 유지 관리 모드를 끝낼 수 있습니다. 각 장치 컨트롤러에 대해 다음 명령을 입력합니다.
   
   `Exit-HcsMaintenanceMode`
5. 유지 관리 모드를 종료하면 컨트롤러가 다시 시작됩니다. 디스크 펌웨어 업데이트가 성공적으로 적용되고 장치가 유지 관리 모드를 종료한 후 Azure 클래식 포털로 돌아갑니다. 유지 관리 모드 업데이트가 설치되었는지 24시간 동안 포털에 표시되지 않을 수도 있습니다.



<!--HONumber=Nov16_HO3-->


