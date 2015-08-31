<properties 
   pageTitle="StorSimple 장치에서 DATA 0 네트워크 인터페이스 설정 수정"
   description="StorSimple 장치에서 DATA 0 네트워크 인터페이스를 다시 구성하는 방법에 대해 알아봅니다."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="alkohli" />

# StorSimple 장치에서 DATA 0 네트워크 인터페이스 설정 수정

## 개요
Microsoft Azure StorSimple 장치에 DATA 0에서 DATA 5까지 6개의 네트워크 인터페이스가 있습니다. DATA 0 인터페이스는 항상 직렬 콘솔 또는 Windows PowerShell 인터페이스를 통해 구성되며 자동으로 클라우드가 활성화됩니다. StorSimple 장치의 초기 배포 중 설치 마법사를 통해 DATA 0 인터페이스가 처음 구성됩니다. 장치가 운영 모드에 있을 때 DATA 0를 다시 구성해야할 수 있습니다. 이 자습서에서는 StorSimple용 Windows PowerShell을 통해 DATA 0 네트워크 설정을 수정하는 두 가지 방법을 제공합니다.

이 자습서를 읽은 후에 다음을 수행할 수 있습니다.

- 설정 마법사를 통해 DATA 0 네트워크 설정 수정
- `Set-HcsNetInterface` cmdlet을 통해 DATA 0 네트워크 설정 수정


## 설정 마법사를 통해 DATA 0 네트워크 설정 수정
StorSimple 장치의 Windows PowerShell 인터페이스에 연결하고 설치 마법사 세션을 실행하여 DATA 0 네트워크 설정을 다시 구성할 수 있습니다. DATA 0 설정을 수정하려면 다음 단계를 수행합니다.

#### 설정 마법사를 통해 DATA 0 네트워크 설정을 수정하려면

1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다. 메시지가 표시되면 **장치 관리자 암호**를 제공합니다. 기본 암호는 `Password1`입니다.

1. 명령 프롬프트에 다음을 입력합니다.


	`Invoke-HcsSetupWizard`

1. 장치의 DATA 0 인터페이스 구성을 도와주는 설치 마법사가 나타납니다. IP 주소, 게이트웨이 및 네트워크 마스크에 대한 새 값을 제공합니다.

> [AZURE.NOTE]Azure 관리 포털에서 StorSimple 장치 **구성** 페이지를 통해 고정된 컨트롤러 IP를 다시 구성해야 합니다. 자세한 내용은 [네트워크 인터페이스 수정](storsimple-modify-device-config.md#modify-network-interfaces)을 참조하세요.


## Set-HcsNetInterface cmdlet을 통해 DATA 0 네트워크 설정 수정
다른 방법은 `Set-HcsNetInterface` cmdlet을 사용하여 DATA 0 네트워크 인터페이스를 다시 구성하는 방법입니다. StorSimple 장치의 Windows PowerShell 인터페이스에서 cmdlet이 실행됩니다. DATA 0 설정을 수정하려면 다음 단계를 수행합니다.

#### Set-HcsNetInterface cmdlet을 통해 DATA 0 네트워크 설정을 수정하려면

1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다. 메시지가 표시되면 장치 관리자 암호를 제공합니다. 기본 암호는 `Password1`입니다.

2. 명령 프롬프트에 다음을 입력합니다.

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
	
    각괄호에 DATA 0에 대해 다음 값을 입력합니다.
											
	- IPv4 주소
	
	- IPv4 게이트웨이
	
	- IPv4 서브넷 마스크
	
	- 컨트롤러 0에 대한 고정 IPv4 주소

	- 컨트롤러 1에 대한 고정 IPv4 주소

## 다음 단계

DATA 0 이외의 네트워크 인터페이스를 구성하려면 [관리 포털에서 페이지 구성](storsimple-modify-device-config.md)을 사용할 수 있습니다. 네트워크 인터페이스를 구성할 때 문제가 발생하는 경우 [배포 문제 해결](storsimple-troubleshoot-deployment.md)을 참조하세요.

<!---HONumber=August15_HO8-->