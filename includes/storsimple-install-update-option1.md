
#### StorSimple용 Windows PowerShell을 통해 업데이트 1을 설치하려면

1. 소프트웨어 업데이트를 다운로드하려면 다음 단계를 수행합니다.

    1. Internet Explorer를 시작하여 [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx)로 이동합니다.
    2. 처음 사용하는 경우 Microsoft 업데이트 카탈로그를 설치하라는 메시지가 표시됩니다. **Install**을 클릭합니다.
    
        ![카탈로그 설치](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. 카탈로그 검색 화면에 표시됩니다. 검색 상자에 **3063418**을 입력하고 **검색**을 클릭합니다.

        ![카탈로그 검색](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. **StorSimple 업데이트 1.0 어플라이언스 업데이트** 번들이 보입니다. **추가**를 클릭합니다. 업데이트는 장바구니에 추가됩니다.

        ![업데이트 번들](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. **바구니 보기**를 클릭합니다.
 
        ![바구니 보기](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. **다운로드**를 클릭합니다. 다운로드를 나타낼 로컬 위치를 지정하거나 검색합니다. 업데이트(all-hcsmdssoftwareupdate\_288da2cc8cd2e3c3958b603a79346cb586fb8fe3.exe)가 선택된 위치에 StorSimple 업데이트 1.0 어플라이언스 업데이트 번들(KB3063418)” 폴더로 다운로드됩니다. 장치에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.
        
2. 소프트웨어 업데이트를 설치하려면 StorSimple 장치 직렬 콘솔에서 Windows PowerShell 인터페이스에 액세스합니다. [PuTTy를 사용하여 직렬 콘솔에 연결](#use-putty-to-connect-to-the-serial-console)에서 자세한 지침을 따르세요.

3. 명령 프롬프트에서 Enter를 누릅니다.

4. **옵션 1**을 선택하여 모든 권한으로 장치에 로그온합니다.

5. 업데이트 패키지를 설치하려면 명령 프롬프트에 다음을 입력합니다.

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    자격 증명 매개 변수는 인증된 공유에 액세스하는 경우에만 사용됩니다.

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

7. Get-HcsUpdateStatus cmdlet을 사용하여 업데이트를 모니터링합니다.

    다음 샘플 출력에서는 진행 중인 업데이트를 보여줍니다.

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     다음 샘플 출력은 업데이트가 완료되었음을 나타냅니다.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
 
8. 소프트웨어 업데이트가 완료되면 유지 관리 포털의 유지 관리 페이지로 이동합니다. 사용 가능한 업데이트를 검색합니다. 추가 소프트웨어 업데이트를 사용할 수 있는지 보여줍니다.

9. **업데이트 설치**를 클릭하여 포털에서 사용 가능한 모든 소프트웨어 업데이트를 적용합니다.

10. 소프트웨어 업데이트를 완료한 후 시스템 소프트웨어, 드라이버 및 펌웨어 버전을 확인합니다. 다음 명령을 입력합니다.

    `Get-HcsSystem`

    다음 버전이 표시되어야 합니다.

    - HcsSoftwareVersion: 6.3.9600.17491
    - CisAgentVersion: 1.0.9037.0
    - MdsAgentVersion: 26.0.4696.1433 
 
11. 펌웨어가 올바르게 업데이트되었는지 확인하려면 다음을 입력합니다.

    `Start-HcsFirmwareCheck`

    펌웨어 상태는 **UpToDate**여야 합니다.

<!---HONumber=August15_HO6-->