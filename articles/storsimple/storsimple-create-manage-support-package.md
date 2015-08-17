<properties 
   pageTitle="지원 패키지 만들기 및 관리| Microsoft Azure"
   description="StorSimple 장치용 지원 패키지 만들기, 암호 해독 및 편집 방법을 알아봅니다."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="alkohli" />


# 지원 티켓 만들기 및 관리

## 개요
이 자습서에서는 지원 패키지 만들기 및 관리와 관련된 다양한 작업을 설명합니다. 지원 패키지는 암호화되고 압축된 형식에 관련된 모든 로그를 포함하며 StorSimple 장치 문제를 해결하는 Microsoft 기술 지원 서비스 팀을 지원하기 위해 사용됩니다.

이 자습서는 다음을 사용하여 지원 패키지를 만들고 관리하도록 단계별 지침을 제공합니다.

- StorSimple 관리자 서비스에서 **유지 관리** 페이지의 **지원 패키지** 섹션
- StorSimple용 Windows PowerShell

이 자습서를 읽은 후에 다음을 수행할 수 있습니다.

- 지원 패키지 만들기
- 지원 패키지 암호 해독 및 편집


## 관리 포털에서 지원 패키지 만들기
StorSimple 관리 서비스에서 발생할 수 있는 문제를 해결하려면 관리 포털에서 서비스의 **유지 관리** 페이지를 통해 Microsoft 지원 사이트에 지원 패키지를 만들고 업로드할 수 있습니다. 업로드를 허용하려면 지원 패스 키를 제공해야 합니다. 지원 패스 키는 지원 엔지니어가 전자 메일로 제공해야 합니다. 암호화되지 않고 압축된 지원 패키지를 생성합니다.(.cab 파일) 이 패키지는 엔지니어가 패스 키를 제공하는 경우 지원 사이트에서 지원 엔지니어가 검색할 수 있습니다.

지원 패키지를 만들려면 관리 포털에서 다음 단계를 수행합니다.

#### 관리 포털에서 지원 패키지 만들려면

1. **장치 > 유지 관리**로 이동합니다.

1. **지원 패키지**섹션에서 **지원 패키지 만들기 및 업로드**를 클릭합니다.

	

1. **지원 패키지 만들기 및 업로드** 대화 상자에서 다음을 수행합니다.

	![지원 패키지 만들기](./media/storsimple-create-manage-support-package/IC740923.png)
											
	- **지원 패스 키**를 제공합니다. 이 키는 Microsoft 지원 엔지니어가 전자 메일로 전송해야 합니다.
 	
	- 동의를 제공하는 콤보 상자를 확인하여 **Microsoft 지원 사이트에 지원 패키지를 자동으로 업로드**합니다.
 	
	- 확인 아이콘![확인 아이콘](./media/storsimple-create-manage-support-package/IC740895.png)을 클릭합니다.


## StorSimple용 Windows PowerShell에서 지원 패키지 만들기
패키지를 만들기 전에 로그 파일을 편집하려면 StorSimple 용 Windows PowerShell을 통해 패키지를 생성해야 합니다.

StorSimple용 Windows PowerShell에서 지원 패키지를 만들려면 다음 단계를 수행합니다.


#### StorSimple용 Windows PowerShell에서 지원 패키지를 만들려면

1. StorSimple 장치에 연결하는데 사용된 원격 컴퓨터에서 관리자 권한으로 Windows PowerShell 세션을 시작하도록 다음 명령을 입력합니다.

	`Start PowerShell`

1. Windows PowerShell 세션에서 장치의 SSAdmin 콘솔 runspace에 연결합니다.


	- 명령 프롬프트에 다음을 입력합니다. 
			
		`$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
		
		
	1. 열린 대화 상자에서 장치 관리자 암호를 입력합니다. 기본 암호는 다음과 같습니다.
	 
		`Password1`

		![SSAdminConsole Runspace에 대한 PowerShell 세션](./media/storsimple-create-manage-support-package/IC740962.png)

	2. **확인**을 클릭합니다.
	1. 명령 프롬프트에 다음을 입력합니다. 
		
		`Enter-PSSession $MS`


1. 열린 세션에서 적절한 명령을 입력합니다.


	- 암호로 보호된 네트워크 공유에 다음을 입력합니다.

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

		암호, 네트워크 공유 폴더의 경로 및 암호화 암호에 대해 묻는 메시지가 나타납니다.(지원 패키지가 암호화 되었기 때문) 이러한 것이 제공되는 경우 지정된 폴더에서 지원 패키지를 만들 수 있습니다.
											

	- 개방된 네트워크 공유 폴더(암호로 보호되지 않은)에 자격 증명 매개 변수가 필요하지 않습니다. 다음을 입력합니다.

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

		지정된 네트워크 공유 폴더에 두 컨트롤러를 위한 지원 패키지를 만들 수 있습니다. 문제해결을 위한 Microsoft 기술 지원 서비스에 보낼 수 있는 암호화되고 압축된 파일입니다. 자세한 내용은 [Microsoft 지원에 문의](https://msdn.microsoft.com/library/dn757750.aspx)를 참조하십시오.


### Export-HcsSupportPackage cmdlet에 대한 자세한 내용
Export-HcsSupportPackage cmdlet과 함께 사용할 수 있는 다른 매개 변수는 아래에서 표로 정리됩니다.

| S. 번호 | 매개 변수 | 필수/선택 | 설명 |
|--------|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Path | 필수 | 지원 패키지가 배치된 네트워크 공유 폴더의 위치를 제공하는 데 사용합니다. |
| 2 | EncryptionPassphrase | 필수 | 지원 패키지를 암호화하기 위해 암호를 제공하는 데 사용합니다. |
| 3 | 자격 증명 | 옵션 | 이 매개 변수를 사용하여 네트워크 공유 폴더에 대한 액세스 자격 증명을 지정합니다. |
| 4 | Force | 옵션 | 암호화 암호 확인 단계를 건너뛰는 데 사용합니다. |
| 5 | PackageTag | 옵션 | 지원 패키지가 배치된 경로의 디렉터리를 지정하는 데 사용합니다. 기본값은 [장치 이름]-[현재 날짜 및 시간: yyyy-MM-dd-HH-mm-ss]입니다. |
| 6 | 범위 | 옵션 | **클러스터**(기본값)으로 지정하여 두 컨트롤러에 대한 지원 패키지를 만듭니다. 현재 컨트롤러에 대해서만 패키지를 만들려면 **컨트롤러**를 지정합니다. |


## 지원 패키지 편집

지원 패키지를 생성한 후에 패키지를 편집하여 로그 파일에서 볼륨 이름, 장치 IP 주소 및 백업 이름과 같은 고객과 관련된 정보를 제거해야 할 수도 있습니다.

> [AZURE.IMPORTANT]StorSimple용 Windows PowerShell을 통해 생성된 지원 패키지를 편집할 수 있습니다. StorSimple 관리 서비스로 관리 포털에서 생성한 패키지를 편집할 수 없습니다.

Microsoft 지원 사이트에 업로드하기 전에 지원 패키지를 편집하려면 지원 패키지의 암호를 해독하고 파일을 편집한 다음 다시 암호화해야 합니다. 지원 패키지를 편집하려면 다음 단계를 수행합니다.

#### StorSimple용 Windows PowerShell에서 지원 패키지를 편집하려면

1. [지원 패키지 생성](#Create-a-Support-package-in-Windows-PowerShell-for-StorSimple)에서 설명한 대로 지원 패키지를 생성하려면 StorSimple용 Windows PowerShell을 사용합니다.

1. [스크립트](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65)를 로컬로 클라이언트에 다운로드합니다.


1. Windows PowerShell 모듈을 가져옵니다. 스크립트를 다운로드한 로컬 폴더로 경로를 지정해야 합니다. 모듈을 가져오려면 다음을 입력합니다.
 
	`Import-module <Path to the folder that contains the Windows PowerShell script>`

1. 지원 패키지 폴더를 엽니다. 모든 파일은 압축 및 암호화된 *.aes* 파일입니다. 파일을 엽니다. 파일을 열려면 다음을 입력합니다.

	`Open-HcsSupportPackage <Path to the folder that contains support package files>`

	파일의 압축을 해제하고 암호를 해독합니다. 실제 파일 확장명은 모든 파일에서 표시됩니다.
	
	![지원 패키지3 편집](./media/storsimple-create-manage-support-package/IC750706.png)


1. 암호화 암호에 대한 메시지가 표시 되는 경우 지원 패키지를 만들 때 사용한 암호를 입력합니다.

    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:EncryptionPassphrase: ****
	

1. 로그 파일이 들어 있는 폴더로 이동합니다. 이제 로그 파일의 압축을 해제하고 암호를 해독하면 원본 파일 확장명을 가질 수 있습니다. 이러한 파일을 수정하여 볼륨 이름 및 장치 IP 주소와 같은 고객 관련 정보를 제거하고 파일을 저장합니다.

1. 파일을 닫습니다. 파일 닫기는 파일을 Gzip으로 압축한 다음 AES-256으로 암호화합니다. 네트워크를 통해 지원 패키지를 전송하는 경우 보안 및 속도를 위해서 이렇게 합니다. 파일을 닫으려면 다음을 입력합니다.

	`Close-HcsSupportPackage <Path to the folder that contains support package files>`

	![지원 패키지2 편집](./media/storsimple-create-manage-support-package/IC750707.png)

1. 메시지가 표시되면 수정된 지원 패키지에 암호화 암호를 제공합니다.

	    cmdlet Close-HcsSupportPackage at command pipeline position 1
    	Supply values for the following parameters:EncryptionPassphrase: ****

1. Microsoft 지원 요청 시 공유할 수 있도록 새 암호를 적어둡니다.


### 예: 암호로 보호된 공유에 대한 지원 패키지에서 파일 편집

지원 패키지의 암호를 해독하고 편집 및 다시 암호화하는 방법을 보여주는 예제는 다음과 같습니다.

![지원 패키지1 편집](./media/storsimple-create-manage-support-package/IC750708.png)

    	PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1
    
    	PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Close-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32>

## 다음 단계

[지원 패키지 및 장치 로그를 사용하여 장치 배포 문제 해결](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting) 방법을 알아봅니다.

<!---HONumber=August15_HO6-->