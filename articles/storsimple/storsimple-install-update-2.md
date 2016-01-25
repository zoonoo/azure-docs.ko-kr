<properties 
   pageTitle="StorSimple 장치에 업데이트 2 설치 | Microsoft Azure"
   description="StorSimple 8000 시리즈 장치에서 StorSimple 8000 시리즈 업데이트 2를 설치하는 방법에 대해 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/12/2016"
   ms.author="alkohli" />

# StorSimple 장치에 업데이트 2 설치

## 개요

이 자습서에서는 Azure 클래식 포털을 통해 이전 소프트웨어 버전을 실행하는 StorSimple 장치에 업데이트 2를 설치하는 방법에 대해 설명합니다. 또한 이 자습서는 StorSimple 장치의 DATA 0 이외의 다른 네트워크 인터페이스에서 게이트웨이를 구성하고 업데이트 1 이전 소프트웨어 버전에서 업데이트를 시도하는 경우 업데이트에 필요한 단계를 다룹니다.

업데이트 2는 장치 소프트웨어 업데이트, LSI 드라이버 업데이트 및 디스크 펌웨어 업데이트를 포함합니다. 장치 소프트웨어 및 LSI 업데이트는 무중단 업데이트이며 Azure 클래식 포털을 통해 적용될 수 있습니다. 디스크 펌웨어 업데이트는 작업 중단 업데이트이며 장치의 Windows PowerShell 인터페이스를 통해서만 적용할 수 있습니다.

> [AZURE.IMPORTANT]
 
> -  업데이트의 단계적 롤아웃을 수행하기 때문에 즉시 업데이트 2를 볼 수는 없습니다. 해당 업데이트를 곧 사용할 수 있게 되므로 몇 일 후에 업데이트를 스캔합니다.
> - 설치하기 전에 일련의 수동 및 자동 전 검사를 수행하며 하드웨어 상태 및 네트워크 연결 측면에서 장치 상태를 확인합니다. Azure 클래식 포털에서 업데이트를 적용하는 경우 이러한 사전 검사를 수행합니다. 
> - Azure 클래식 포털을 통해 소프트웨어 및 드라이버 업데이트를 설치하는 것이 좋습니다. 포털에서 사전 업데이트 게이트웨이 검사가 실패한 경우 (업데이트를 설치하려면) 장치의 Windows PowerShell 인터페이스로 이동해야 합니다. 업데이트를 설치하는 데 4-7시간이 걸릴 수 있습니다(Windows 업데이트 포함). 장치의 Windows PowerShell 인터페이스를 통해 유지 관리 모드 업데이트를 설치해야 합니다. 유지 관리 모드 업데이트는 중단 업데이트입니다. 이는 장치에 중단 시간을 발생시킵니다.
> - 선택적 StorSimple Snapshot Manager를 실행하는 경우 장치를 업데이트하기 전에 Snapshot Manager 버전을 업데이트 2로 업그레이드했는지 확인합니다.

## 업데이트 준비
업데이트를 스캔하고 적용하기 전에 다음 단계를 수행해야 합니다.


1. 장치 데이터의 클라우드 스냅숏을 만듭니다.

2. 컨트롤러 고정 IP가 라우팅할 수 있으며 인터넷에 연결할 수 있는지 확인합니다. 이러한 고정된 IP는 장치를 서비스 업데이트하는 데 사용됩니다. 장치의 Windows PowerShell 인터페이스에서 다음 cmdlet을 실행하여 이를 테스트할 수 있습니다.

 	`Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
 
	**고정된 IP가 인터넷에 연결할 수 있는 경우 테스트 연결에 대한 샘플 출력**

	    
		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com
	    
	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	
		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200

이러한 수동 사전 검사를 성공적으로 완료한 후에 업데이트 검색 및 설치를 진행할 수 있습니다.

## Azure 클래식 포털을 통해 업데이트 2 설치 

장치를 업데이트하기 위해 권장되는 절차입니다. 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

## 핫픽스로 업데이트 2 설치 

Azure 클래식 포털을 통해 업데이트를 설치하려고 할 때 게이트웨이 검사에 실패하는 경우에만 이 절차를 사용합니다. 비데이터 0 네트워크 인터페이스에 할당된 게이트웨이가 있고 장치가 업데이트 1 이전의 소프트웨어 버전을 실행하는 경우 확인이 실패합니다.

핫픽스 메서드를 사용하여 업그레이드할 수 있는 소프트웨어 버전은 업데이트 0.1, 업데이트 0.2, 업데이트 0.3, 업데이트 1, 업데이트 1.1, 업데이트 1.2입니다. 핫픽스 메서드에는 다음 세 단계가 포함됩니다.

- Microsoft Update 카탈로그에서 핫픽스를 다운로드합니다.
- 일반 모드 핫픽스를 설치 및 확인합니다.
- 유지 관리 모드 핫픽스를 설치 및 확인합니다.

이 메서드를 통해 적용된 핫픽스는 아래에서 표로 정리되어 있습니다.

| 순서 | KB | 이름 | 패키지 설명 | 업데이트 유형 |
|--------|-----------|-------------------------|-----------------------------|-------------|
| 1 | KB3121901 | 소프트웨어 업데이트 | HcsMdsSfotwareUpdate.exe <br></br> CisMdsAgentUpdateBundle.exe | 일반 |
| 2 | KB3121900 | LSI 드라이버 | HcsLsiUpdate.exe | 일반 |
| 3 | KB3080728 | Storport 수정 | Storport-KB3080728-x64.msu | 일반 |
| 4 | KB3090322 | Spaceport 수정 | Spaceport-KB3090322-x64.msu | 일반 |
| 5 | KB3121899 | 디스크 펌웨어 | DiskFirmwarePackage.exe | 유지 관리 |


> [AZURE.IMPORTANT]
> 
> - 장치가 릴리스(GA) 버전을 실행하는 경우 이 업데이트 설치를 위한 지원을 받으려면 [Microsoft 지원에 문의](storsimple-contact-microsoft-support.md)하세요.
> - 이 절차는 업데이트 2에만 적용하도록 한 번만 수행해야 됩니다. Azure 클래식 포털을 사용하여 후속 업데이트를 적용할 수 있습니다.
> - 각 핫픽스 설치를 완료하려면 약 20분 정도 걸릴 수 있습니다. 총 설치 시간은 2시간에 가깝습니다. 
> - 이 절차에 따라 업데이트를 적용하기 전에 두 장치 컨트롤러가 온라인 상태이고 모든 하드웨어 구성 요소가 정상 상태에 있는지를 확인합니다.

다음 단계를 수행하여 업데이트 2를 핫픽스로 적용합니다.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]


## 업데이트 오류 문제 해결

**업그레이드 전 검사에 실패했다는 알림을 보는 경우 어떻게 되나요?**

사전 검사가 실패하면 페이지의 맨 아래에 있는 자세한 알림 표시줄에 있는지 확인합니다. 이 사전 검사 실패와 관련된 지침을 제공합니다. 다음 그림에서는 이러한 알림이 표시되는 인스턴스를 보여줍니다. 이 경우에 컨트롤러 상태 확인 및 하드웨어 구성 요소 상태 검사에 실패했습니다. **하드웨어 상태** 섹션에서, **컨트롤러 0**과 **컨트롤러 1** 구성 요소 모두에 주의가 필요한 지 볼 수 있습니다.
 
  ![사전 검사 실패](./media/storsimple-install-update-2/HCS_PreUpdateCheckFailed-include.png)

두 컨트롤러가 정상이고 온라인 상태인지 확인해야 합니다. 유지 관리 페이지에서 StorSimple 장치에서 모든 하드웨어 구성 요소가 정상으로 표시되어 있는지도 확인해야 합니다. 그런 다음 업데이트를 설치할 수 있습니다. 하드웨어 구성 요소 문제를 해결할 수 없는 경우, 다음 단계는 Microsoft 지원에 문의해야 합니다.

**"업데이트를 설치하지 못했습니다" 오류 메시지를 수신하고 업데이트 문제 해결 가이드를 참조하여 실패의 원인을 확인하는 것이 좋은 경우 어떻게 되나요?**

가능한 원인 중 하나는 Microsoft 업데이트 서버에 연결되지 않은 것일 수 있습니다. 수행해야 하는 수동 검사입니다. 업데이트 서버에 대한 연결이 손실된 경우 업데이트 작업이 실패합니다. StorSimple 장치의 Windows PowerShell 인터페이스에서 다음 cmdlet을 실행하여 연결을 확인할 수 있습니다.

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

두 컨트롤러에서 cmdlet을 실행합니다.
 
연결되었음을 확인했으나 계속 이 문제가 발생하는 경우, 다음 단계는 Microsoft 지원에 문의하세요.


## 다음 단계

[업데이트 2 릴리스](storsimple-update2-release-notes.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0114_2016-->