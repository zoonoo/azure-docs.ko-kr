<!--author=SharS last changed: 11/16/15-->

#### StorSimple용 Windows PowerShell을 통해 업데이트 1.2를 설치하려면

1. 소프트웨어 업데이트를 다운로드하려면 다음 단계를 수행합니다.

    1. Internet Explorer를 시작하여 [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx)로 이동합니다.
    2. 처음 사용하는 경우 Microsoft 업데이트 카탈로그를 설치하라는 메시지가 표시됩니다. **Install**을 클릭합니다.
    
        ![카탈로그 설치](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. 카탈로그 검색 화면에 표시됩니다. 검색 상자에 **3063418**을 입력하고 **검색**을 클릭합니다.

        ![카탈로그 검색](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. **StorSimple 업데이트 1.2 어플라이언스 업데이트** 번들이 표시됩니다. **추가**를 클릭합니다. 업데이트는 장바구니에 추가됩니다.

        ![업데이트 번들](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. **바구니 보기**를 클릭합니다.
 
        ![바구니 보기](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. **다운로드**를 클릭합니다. 다운로드를 표시할 로컬 위치를 지정하거나 **찾습니다**. 업데이트가 **StorSimple 업데이트 1.2 어플라이언스 업데이트 번들**(KB3063418) 폴더의 선택한 위치에 다운로드됩니다. 장치에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.
    
	이 절차에서는 소프트웨어 장치 업데이트를 핫픽스로 설치하고, Microsoft 업데이트 서버에서 디스크 펌웨어 업데이트를 설치하고, Azure 포털에서 LSI 드라이버 및 Windows 업데이트를 설치하는 방법을 설명합니다. 그러나 소프트웨어, 드라이버 및 디스크 펌웨어 업데이트를 모두 핫픽스로 설치하도록 선택할 수 있습니다. 그런 다음 StorSimple 1.2 SAS 컨트롤러 업데이트(KB3043005) 및 StorSimple 1.2 디스크 펌웨어 업데이트(KB3063416)를 다운로드하고 동일한 공유 폴더에 복사해야 합니다. 디스크 펌웨어 업데이트를 핫픽스로 설치하려면 [StorSimple용 Windows PowerShell을 통해 유지 관리 모드 핫픽스 설치](storsimple-update-device.md#install-hotfixes-via-windows-powershell-for-storsimple)의 지침을 따르세요.
    
	> [AZURE.NOTE]피어 컨트롤러의 잠재적 오류 메시지를 검색하려면 두 컨트롤러에서 모두 핫픽스에 액세스할 수 있어야 합니다.
            
2. 소프트웨어 업데이트를 설치하려면 StorSimple 장치 직렬 콘솔에서 Windows PowerShell 인터페이스에 액세스합니다. [PuTTy를 사용하여 직렬 콘솔에 연결](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)에서 자세한 지침을 따르세요.

3. 명령 프롬프트에서 **Enter** 키를 누릅니다.

4. **옵션 1**을 선택하여 모든 권한으로 장치에 로그온합니다.

5. 업데이트 패키지를 설치하려면 명령 프롬프트에 다음을 입력합니다.

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    위 명령에서 공유 경로에 DNS 대신 IP를 사용합니다. 자격 증명 매개 변수는 인증된 공유에 액세스하는 경우에만 사용됩니다.

	자격 증명 매개 변수를 사용하여 공유에 액세스하는 것이 좋습니다. 일반적으로 "모든 사용자"에게 개방된 공유도 인증되지 않은 사용자에게는 개방되지 않습니다.

    샘플 출력은 다음과 같습니다.

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. 핫픽스 설치를 확인하라는 메시지가 표시되면 **Y**를 입력합니다.

7. `Get-HcsUpdateStatus` cmdlet을 사용하여 업데이트를 모니터링합니다.

    다음 샘플 출력에서는 진행 중인 업데이트를 보여줍니다. 업데이트가 진행 중이면 `RunInprogress`가 `True`입니다.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     다음 샘플 출력은 업데이트가 완료되었음을 나타냅니다. 업데이트가 완료되면 `RunInProgress`가 `False`입니다.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE]업데이트가 진행 중일 때 cmdlet에서 `False`를 보고하는 경우도 있습니다. 핫픽스가 완료되었는지 확인하려면 몇 분 동안 기다린 후 이 명령을 다시 실행하고 `RunInProgress`가 `False`인지 확인합니다. 맞으면 핫픽스가 완료된 것입니다.
	
8. 소프트웨어 업데이트가 완료된 후 시스템 소프트웨어 버전을 확인합니다. 다음 명령을 입력합니다.

    `Get-HcsSystem`

    다음 버전이 표시되어야 합니다.

    - HcsSoftwareVersion: 6.3.9600.17584
    - CisAgentVersion: 1.0.9049.0
    - MdsAgentVersion: 26.0.4696.1433 
    
	업데이트를 적용한 후 버전 번호가 변경되지 않으면 핫픽스를 적용하지 못한 것입니다. 이 경우 추가 지원을 받으려면 [Microsoft 지원](storsimple-contact-microsoft-support.md)에 문의하세요.
    
9. 이제 완료하는 데 약 30-45분이 걸리는 작업 중단 디스크 펌웨어 업데이트를 설치하겠습니다. 장치 직렬 콘솔에 연결하여 계획된 유지 관리 기간에 설치하도록 선택할 수 있습니다. 디스크 펌웨어 업데이트를 설치하려면 [StorSimple용 Windows PowerShell을 통해 유지 관리 모드 업데이트 설치](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)의 지침을 따르세요.

10. 디스크 펌웨어 업데이트가 성공적으로 적용되고 장치가 유지 관리 모드를 종료한 후 Azure 포털로 돌아갑니다. 유지 관리 모드 업데이트는 24시간이 경과할 때까지 포털에서 업데이트되지 않습니다. Azure 포털에서 나머지 무중단 업데이트를 적용하기 전에 기다려야 할 수도 있습니다.

11. 업데이트를 적용할 준비가 되면 **유지 관리** 페이지로 이동한 다음 페이지의 맨 아래에서 **업데이트 검색**을 클릭합니다. 업데이트를 사용할 수 있으면 알림이 표시되며, 여기에는 드라이버 및 Windows 업데이트가 포함됩니다. **업데이트 설치**를 클릭하여 설치를 시작합니다. 모든 업데이트가 성공적으로 설치되면 완료되었습니다.





 
 

<!---HONumber=Nov15_HO4-->