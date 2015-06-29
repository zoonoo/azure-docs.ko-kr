<properties 
   pageTitle="StorSimple 장치에 업데이트 1을 설치합니다."
   description="장치에서 StorSimple 8000 시리즈 업데이트 1을 설치하는 방법에 대해 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/18/2015"
   ms.author="alkohli" />

# StorSimple 장치에 업데이트 1을 설치합니다.

## 개요

이 자습서에서는 업데이트 1 이전 소프트웨어 버전을 실행하는 StorSimple 장치에 업데이트 1을 설치하는 방법에 대해 설명합니다. 장치는 일반적으로 사용 가능한(GA) 릴리스, 업데이트 0.1, 업데이트 0.2 또는 업데이트 0.3 소프트웨어를 실행 중일 수 있습니다.

이 설치 중 장치가 업데이트 1 이전 버전을 실행 중인 경우 장치에서 검사가 수행됩니다. 이러한 검사는 하드웨어 상태 및 네트워크 연결 측면에서 장치 상태를 확인합니다.

수동으로 사전 검사를 수행하는지 묻는 메시지가 나타납니다.

- 컨트롤러 고정 IP는 라우팅할 수 있으며 인터넷에 연결할 수 있습니다. 이 IP는 StorSimple 장치에 대한 업데이트를 처리하는데 사용됩니다. 각 컨트롤러에서 다음 cmdlet을 실행하여 테스트할 수 있습니다.

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
	    
	    


- 장치를 업데이트하기 전에 장치 데이터의 클라우드 스냅숏을 작성하는 것이 좋습니다.

수동 검사를 확인하고 승인한 후에 일련의 자동 업데이트 전 검사가 수행됩니다. 내용은 다음과 같습니다.

- **컨트롤러 상태 검사** 장치 컨트롤러가 정상이고 온라인에 있는지 모두 확인합니다.

- **하드웨어 구성 요소 상태 검사** StorSimple 장치에서 모든 하드웨어 구성 요소가 정상인지 확인합니다.

- **데이터 0 검사** 데이터 0이 장치에서 사용할 수 있는지 확인합니다. 이 인터페이스를 사용하지 않는 경우 다음을 사용하도록 설정하고 다시 시도해야 합니다.

- **데이터 2 및 데이터 3 검사** 데이터 2와 데이터 3 네트워크 인터페이스를 사용하지 않도록 설정했는지 확인합니다. 이 인터페이스를 사용하는 경우 사용하지 않도록 설정한 다음 장치를 업데이트해야 합니다. 이 검사는 GA 소프트웨어를 실행하는 장치에서 업데이트 하는 경우에 수행됩니다. 0.1, 0.2, 또는 0.3 버전을 실행하는 장치에는 이 확인이 필요하지 않습니다.

- **게이트웨이 검사** 모든 장치에서 업데이트 1 이전 버전을 실행합니다. 이 검사는 데이터 0 이외의 다른 네트워크 인터페이스에 대해 게이트웨이가 구성된 장치에만 수행됩니다.
 
모든 사전 업데이트 검사를 성공적으로 완료하는 경우에만 업데이트 1이 적용됩니다. 업데이트 1를 StorSimple 장치에 적용한 후 데이터 2 및 데이터 3 검사 및 게이트웨이 검사가 이후 업데이트에 포함되지 않습니다. 다른 사전 검사가 수행됩니다.

## 관리 포털을 사용하여 업데이트 1을 설치합니다.

GA 버전을 실행하는 장치를 업데이트 하려면 Azure 관리 포털을 사용하는 것이 좋습니다. 다음 단계를 수행하여 장치를 업데이트합니다.

[AZURE.INCLUDE [storsimple-install-update-via-portal](../../includes/storsimple-install-update-via-portal.md)]


## 업데이트 오류 문제 해결

**업그레이드 전 검사에 실패했다는 알림을 보는 경우 어떻게 되나요?**

사전 검사가 실패하면 페이지의 맨 아래에 있는 자세한 알림 표시줄에 있는지 확인합니다. 이 사전 검사 실패와 관련된 지침을 제공합니다. 다음 그림에서는 이러한 알림이 표시되는 인스턴스를 보여줍니다. 이 경우에 컨트롤러 상태 확인 및 하드웨어 구성 요소 상태 검사에 실패했습니다. **하드웨어 상태** 섹션에서, 컨트롤러 0과 컨트롤러 1 구성 요소 모두에 주의가 필요한 지 볼 수 있습니다.
 
  ![사전 검사 실패](./media/storsimple-install-update-1/HCS_PreUpdateCheckFailed-include.png)

두 컨트롤러가 정상이고 온라인 상태인지 확인해야 합니다. 유지 관리 페이지에서 StorSimple 장치에서 모든 하드웨어 구성 요소가 정상으로 표시되어 있는지도 확인해야 합니다. 그런 다음 업데이트를 설치할 수 있습니다. 하드웨어 구성 요소 문제를 해결할 수 없는 경우, 다음 단계는 Microsoft 지원에 문의해야 합니다.

**"업데이트를 설치하지 못했습니다" 오류 메시지를 수신하고 업데이트 문제 해결 가이드를 참조하여 실패의 원인을 확인하는 것이 좋은 경우 어떻게 되나요?**

가능한 원인 중 하나는 Microsoft 업데이트 서버에 연결되지 않은 것일 수 있습니다. 수행해야 하는 수동 검사입니다. 업데이트 서버에 대한 연결이 손실된 경우 업데이트 작업이 실패합니다. StorSimple 장치의 Windows PowerShell 인터페이스에서 다음 cmdlet을 실행하여 연결을 확인할 수 있습니다.

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

두 컨트롤러에서 cmdlet을 실행합니다.
 
연결되었음을 확인했으나 계속 이 문제가 발생하는 경우, 다음 단계는 Microsoft 지원에 문의하세요.

## 다음 단계

[Microsoft Azure StorSimple](storsimple-overview.md)에 대해 자세히 알아봅니다.

<!---HONumber=58_postMigration-->