<!--author=alkohli last changed: 01/12/15-->

#### 핫픽스를 다운로드하려면

Microsoft 업데이트 카탈로그에서 소프트웨어 업데이트를 다운로드하려면 다음 단계를 수행합니다.

1. Internet Explorer를 시작하여 [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx)로 이동합니다.

2. 처음 사용하는 경우 Microsoft 업데이트 카탈로그를 설치하라는 메시지가 표시됩니다. **Install**을 클릭합니다.
    
   	![카탈로그 설치](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. 카탈로그 검색 화면에 표시됩니다. 검색 상자에 **3121901**을 입력하고 **검색**을 클릭합니다.

    ![카탈로그 검색](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. **StorSimple 8000 시리즈에 대한 누적 소프트웨어 번들 업데이트 2.0**이 표시됩니다. **추가**를 클릭합니다. 업데이트는 장바구니에 추가됩니다.

5. **바구니 보기**를 클릭합니다.
 
6. **다운로드**를 클릭합니다. 다운로드를 표시할 로컬 위치를 지정하거나 **찾습니다**. 업데이트가 업데이트와 같은 이름으로 폴더의 선택한 위치에 다운로드됩니다. 장치에서 연결할 수 있는 네트워크 공유에 폴더도 복사할 수 있습니다.
       
	> [AZURE.NOTE]
	> 
	> - 또한 **LSI 드라이버 업데이트**(StorSimple 8000 시리즈용 SAS 컨트롤러 업데이트 2.0 - KB3121900), **Storport 업데이트**(Windows Server 2012 R2 x64 Edition용 핫픽스 - KB3080728), **Spaceport 업데이트**(Windows Server 2012 R2 x64 Edition용 핫픽스 - KB3090322) 및 **디스크 펌웨어 업데이트**(StorSimple 8000 시리즈용 누적 디스크 펌웨어 업데이트 2.0 - KB3121899)도 다운로드하여 동일한 공유 폴더에 복사해야 합니다.
	> - 피어 컨트롤러의 잠재적 오류 메시지를 검색하려면 두 컨트롤러에서 모두 핫픽스에 액세스할 수 있어야 합니다.

#### 일반 모드 핫픽스를 설치 및 확인하려면

일반 핫픽스를 설치 및 확인하려면 다음 단계를 수행합니다.

1. 핫픽스를 설치하려면 StorSimple 장치 직렬 콘솔에서 Windows PowerShell 인터페이스에 액세스합니다. [PuTTy를 사용하여 직렬 콘솔에 연결](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)에서 자세한 지침을 따르세요. 명령 프롬프트에서 **Enter** 키를 누릅니다.

4. **옵션 1**을 선택하여 모든 권한으로 장치에 로그온합니다.

5. 핫픽스를 설치하려면 명령 프롬프트에 다음을 입력합니다.

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
        LastHotfixTimestamp : 12/21/2015 10:36:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     다음 샘플 출력은 업데이트가 완료되었음을 나타냅니다. 업데이트가 완료되면 `RunInProgress`가 `False`입니다.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 12/21/2015 10:59:13 PM
        LastUpdateTimestamp : 12/21/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE]업데이트가 진행 중일 때 cmdlet에서 `False`를 보고하는 경우도 있습니다. 핫픽스가 완료되었는지 확인하려면 몇 분 동안 기다린 후 이 명령을 다시 실행하고 `RunInProgress`가 `False`인지 확인합니다. 맞으면 핫픽스가 완료된 것입니다.
	
8. 소프트웨어 업데이트가 완료되었으면 `CisMdsAgentUpdateBundle.exe`를 사용하여 3-5단계를 반복하고 SaaS 에이전트 및 MDS 에이전트를 설치 및 모니터링합니다. `CisMdsAgentUpdateBundle.exe` 이전에 `HcsMdsSoftwareUpdate.exe`가 설치되었는지 확인합니다.

9. 시스템 소프트웨어 버전을 확인합니다. 형식:

    `Get-HcsSystem`

    다음 버전이 표시되어야 합니다.

    - HcsSoftwareVersion: 6.3.9600.17673
    - CisAgentVersion: 1.0.9150.0
    - MdsAgentVersion: 30.0.4698.13 
    
	업데이트를 적용한 후 버전 번호가 변경되지 않으면 핫픽스를 적용하지 못한 것입니다. 이 경우 추가 지원을 받으려면 [Microsoft 지원](storsimple-contact-microsoft-support.md)에 문의하세요.
    
9. 나머지 일반 핫픽스를 설치 및 모니터링하려면 3-5단계를 반복합니다.

	- `HcsLsiUpdate.exe` 패키지를 사용하여 LSI 드라이버(KB3121900).
	- `Storport-KB3080728-x64.msu` 패키지를 사용하여 Storport 수정(KB3080728).
	- `spaceport-KB3090322-x64.msu` 패키지를 사용하여 Spaceport 수정(KB3090322).

#### 유지 관리 모드 핫픽스를 설치 및 확인하려면

`DiskFirmwarePackage.exe` 패키지(KB3121899)를 사용하여 디스크 펌웨어 업데이트를 설치합니다. 작업 중단 업데이트이며 완료하는 데 약 30분이 소요됩니다. 장치 직렬 콘솔에 연결하여 계획된 유지 관리 기간에 설치하도록 선택할 수 있습니다. 디스크 펌웨어 업데이트를 설치하려면 아래 지침을 따릅니다.

1. 장치를 유지 관리 모드로 설정합니다. 유지 관리 모드에서 장치에 연결할 때는 Windows PowerShell 원격을 사용해서는 안 됩니다. 장치 직렬 콘솔을 통해 연결된 경우 장치 컨트롤러에서 이 cmdlet을 실행해야 합니다. 형식:
		
	`Enter-HcsMaintenanceMode`

	샘플 출력은 다음과 같습니다.

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update2-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17664
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------

		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	두 컨트롤러가 모두 다시 부팅됩니다. 재부팅이 완료되면 두 컨트롤러는 유지 관리 모드가 됩니다.

3. 디스크 펌웨어 업데이트를 설치하려면 다음을 입력합니다.

	`Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

	샘플 출력은 다음과 같습니다.

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
		Enter Password:
		WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
		Confirm
		This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
		[Y] Yes [N] No (Default is "Y"): Y
		WARNING: Installation is currently in progress. This operation can take several minutes to complete.
	

1.  `Get-HcsUpdateStatus` 명령을 사용하여 설치 진행률을 모니터합니다. `RunInProgress`가 `False`로 변경되면 업데이트가 완료된 것입니다.
 
2.  설치가 완료된 후에 유지 관리 모드 핫픽스가 설치된 컨트롤러가 다시 부팅됩니다. 모든 권한이 있는 옵션 1로 로그인하고 디스크 펌웨어 버전을 확인합니다. 형식:
	
	`Get-HcsFirmwareVersion`
  
	예상된 디스크 펌웨어 버전은 다음과 같습니다.

	`XMGG, XGEG, KZ50, F6C2, VR08`

	샘플 출력은 다음과 같습니다.


        -----------------------MAINTENANCE MODE------------------------
    	Microsoft Azure StorSimple Appliance Model 8100
    	Name: Update2-8100-SHG0997879L76YD
    	Software Version: 6.3.9600.17664
    	Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    	You are connected to Controller1
    	---------------------------------------------------------------
    	
    	Controller1>Get-HcsFirmwareVersion
    	
    	
    	Controller0 : TalladegaFirmware
    	  ActiveBIOS:0.45.0006
    	  BackupBIOS:0.45.0008
    	  MainCPLD:17.0.0005
    	  ActiveBMCRoot:2.0.000E
    	  BackupBMCRoot:2.0.000E
    	  BMCBoot:2.0.0001
    	  LsiFirmware:19.00.00.00
    	  LsiBios:07.37.00.00
    	  Battery1Firmware:06.29
    	  Battery2Firmware:06.29
    	  DomFirmware:X231600
    	  CanisterFirmware:3.5.0.32
    	  CanisterBootloader:5.03
    	  CanisterConfigCRC:0xD1B030A4
    	  CanisterVPDStructure:0x06
    	  CanisterGEMCPLD:0x17
    	  CanisterVPDCRC:0xEE3504B4
    	  MidplaneVPDStructure:0x0C
    	  MidplaneVPDCRC:0xA6BD4F64
    	  MidplaneCPLD:0x10
    	  PCM1Firmware:1.00|1.05
    	  PCM1VPDStructure:0x05
    	  PCM1VPDCRC:0x41BEF99C
    	  PCM2Firmware:1.00|1.05
    	  PCM2VPDStructure:0x05
    	  PCM2VPDCRC:0x41BEF99C
    	
    	  DisksFirmware
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST400FM0073:XGEG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG
    	  SEAGATE:ST4000NM0023:XMGG

	 두 번째 컨트롤러에서 `Get-HcsFirmwareVersion` 명령을 실행하여 해당 소프트웨어 버전이 업데이트되었는지 확인합니다. 그런 다음 유지 관리 모드를 끝낼 수 있습니다. 각 장치 컨트롤러에 대해 다음 명령을 입력합니다.

    `Exit-HcsMaintenanceMode`
     
1. 유지 관리 모드를 종료하면 컨트롤러가 다시 부팅됩니다. 디스크 펌웨어 업데이트가 성공적으로 적용되고 장치가 유지 관리 모드를 종료한 후 Azure 클래식 포털로 돌아갑니다. 유지 관리 모드 업데이트는 24시간이 경과할 때까지 포털에서 업데이트되지 않습니다.






 
 

<!---HONumber=AcomDC_0114_2016-->