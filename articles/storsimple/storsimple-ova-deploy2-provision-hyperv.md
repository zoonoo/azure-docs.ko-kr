<properties
   pageTitle="StorSimple 가상 배열 배포 - Hyper-V에서 프로비전"
   description="StorSimple 가상 배열 배포에 대한 두 번째 자습서에는 Hyper-V에서 가상 장치를 프로비전하는 내용이 포함됩니다."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="alkohli"/>

# StorSimple 가상 배열 배포 - Hyper-V에서 가상 배열 프로비전

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## 개요

이 프로비전 자습서는 2016년 3월 GA(일반 공급) 버전을 실행하는 Microsoft Azure StorSimple 가상 배열(StorSimple 온-프레미스 가상 장치 또는 StorSimple 가상 장치라고도 함)에 적용됩니다. 이 자습서는 Hyper-V 2008 R2, Hyper-V 2012 또는 Hyper-V 2012 R2를 실행하는 호스트 시스템에 StorSimple 가상 배열을 프로비전하는 방법을 설명합니다. 이 문서는 Microsoft Azure Government 클라우드뿐만 아니라 Azure 클래식 포털에서 StorSimple 가상 배열의 배포에 적용됩니다.

가상 장치를 프로비전하고 구성하려면 관리자 권한이 필요합니다. 프로비전 및 초기 설정을 완료하는 데 10분 정도가 소요됩니다.


## 프로비전 필수 조건

여기에는 Hyper-V 2008 R2, Hyper-V 2012 또는 Hyper-V 2012 R2를 실행하는 호스트 시스템에 가상 장치를 프로비전하기 위한 필수 조건이 제공됩니다.

### StorSimple 관리자 서비스의 경우

시작하기 전에 다음 사항을 확인합니다.

-   [StorSimple 가상 배열용 포털 준비](storsimple-ova-deploy1-portal-prep.md)의 모든 단계를 완료했습니다.

-   Azure 포털에서 Hyper-V용 가상 장치 이미지를 다운로드했습니다. 자세한 내용은 [3단계: 가상 장치 이미지 다운로드](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image)를 참조하세요.

	> [AZURE.IMPORTANT] StorSimple 가상 배열에서 실행되는 소프트웨어는 Storsimple Manager 서비스와 함께 사용해야 합니다.

### StorSimple 가상 장치의 경우

가상 장치를 배포하기 전에 다음 사항을 확인해야 합니다.

-   장치 프로비전에 사용될 수 있는 Hyper-V(2008 R2 이상)를 실행하는 호스트 시스템에 액세스할 수 있습니다.

-   가상 디스크 프로비전을 위해 호스트 시스템에서 다음 리소스를 전용할 수 있습니다.

	-   코어 4개 이상

	-   RAM 8GB 이상

	-   네트워크 인터페이스 하나

	-   시스템 데이터용 가상 디스크 500GB

### 데이터 센터에서 네트워크의 경우

시작하기 전에 다음 사항을 확인합니다.

-   StorSimple 가상 장치를 배포하기 위한 네트워킹 요구 사항을 검토하고, 요구 사항에 따라 데이터 센터 네트워크를 구성했습니다. 자세한 내용은 [StorSimple 가상 배열 네트워킹 요구 사항](storsimple-ova-system-requirements.md#networking-requirements)을 참조하세요.

## 단계별 프로비전

가상 장치를 프로비전하고 연결하려면 다음 단계를 수행해야 합니다.

1.  호스트 시스템에 최소 가상 장치 요구 사항을 충족하기에 충분한 리소스가 있는지 확인합니다.

2.  하이퍼바이저에서 가상 장치를 프로비전합니다.

3.  가상 장치를 시작하고 IP 주소를 가져옵니다.

각 단계는 다음 섹션에서 설명합니다.

## 1단계: 호스트 시스템이 최소 가상 장치 요구 사항을 충족하도록 합니다.

가상 장치를 만들려면 다음이 필요합니다.

-   Windows Server 2008 R2 SP1, Windows Server 2012 또는 Windows Server 2012 R2에서 실행되는 Hyper-V 2008 R2 SP1, Hyper-V 2012 또는 Hyper-V 2012 R2

-   호스트에 연결된 Microsoft Windows 클라이언트의 Microsoft Hyper-V 관리자

가상 장치를 만드는 기본 하드웨어(호스트 시스템)에서 가상 장치에 대해 다음 리소스를 전용할 수 있도록 해야 합니다.

- 코어 4개 이상
- RAM 8GB 이상
- 네트워크 인터페이스 하나
- 시스템 데이터용 가상 디스크 500GB

## 2단계: 하이퍼바이저에서 가상 장치를 프로비전합니다.

하이퍼바이저에서 장치를 프로비전하려면 다음 단계를 수행합니다.

#### 가상 장치를 프로비전하려면

1.  Windows Server 호스트에서 로컬 드라이브에 가상 장치 이미지를 복사합니다. 이는 Azure 포털을 통해 다운로드해 놓은 이미지(VHD 또는 VHDX)입니다. 이미지를 복사한 위치를 나중에 나오는 단계에서 사용할 수 있도록 기록해 둡니다.

2.  **서버 관리자**를 엽니다. 오른쪽 위 모서리에서 **도구**를 클릭하고 **Hyper-V 관리자**를 선택합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

	Hyper-V 2008 R2를 실행하는 경우에는 Hyper-V 관리자를 엽니다. 서버 관리자에서 **역할 > Hyper-V > Hyper-V 관리자**를 클릭합니다.

1.  **Hyper-V 관리자**의 범위 창에서 시스템 노드를 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 엽니다. **새로 만들기**를 선택한 후 **가상 컴퓨터**를 선택합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  **시작하기 전에** 페이지에서 **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image3.png)

1.  **이름 및 위치 지정** 페이지에서 가상 장치의 **이름**을 입력합니다. **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  **세대 지정** 페이지에서 VHD를 사용하는 경우 **1세대**를 선택합니다. VHDX(Windows Server 2012 이상)를 사용하는 경우 **2세대**를 선택합니다. **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

	Hyper-V 2008 R2를 실행하는 경우에는 이 화면이 표시되지 않습니다.

1.  **메모리 할당** 페이지에서:

    a. **시작 메모리**를 8192MB 이상으로 지정합니다. StorSimple 가상 장치에 대한 최소 메모리 요구 사항은 8GB 이상입니다. **이 가상 컴퓨터에 동적 메모리를 사용합니다.** 옵션을 선택하지 않습니다.

    b. **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  **네트워킹 구성** 페이지에서:

    a. **연결** 드롭다운 목록에서 가상 스위치를 선택합니다. 인터넷에 연결된 가상 스위치를 선택해야 합니다.

    b. **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  **가상 하드 디스크 연결** 페이지에서:

    a. **기존 가상 하드 디스크 사용** 옵션을 선택합니다. 호스트 시스템에 다운로드한 VHD를 가리킵니다.

    b. **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  표시되는 **요약**을 검토합니다. **마침**을 클릭하여 가상 컴퓨터를 만듭니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  최소 요구 사항을 충족하려면 코어 4개가 필요합니다. 4개의 가상 프로세서를 추가하려면 **Hyper-V 관리자** 창에 선택된 호스트 시스템에서, **가상 컴퓨터** 목록 아래 있는 오른쪽 창에서 방금 만든 가상 컴퓨터를 찾습니다. 컴퓨터 이름을 선택하고 마우스 오른쪽 단추로 클릭한 후 **설정**을 선택합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  **설정** 페이지의 왼쪽 창에서 **프로세서**를 클릭합니다. 오른쪽 창에서 **가상 프로세서 수**를 4(또는 그 이상)로 설정합니다. **Apply**를 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  최소 요구 사항을 충족하려면 가상 데이터 디스크를 500GB 추가해야 합니다. **설정** 페이지에서:

    1.  왼쪽 창의 **SCSI 컨트롤러**를 선택합니다.
    2.  오른쪽 창의 **하드 드라이브**를 선택하고 **추가**를 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  **하드 드라이브** 페이지에서 **가상 하드 디스크** 옵션을 선택하고 **새로 만들기**를 클릭합니다. **새 가상 하드 디스크 마법사**가 시작됩니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  **시작하기 전에** 페이지에서 **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image14.png)

1.  **디스크 형식 선택** 페이지에서 **VHDX** 형식의 기본 옵션을 적용합니다. **다음**을 클릭합니다. Hyper-V 2008 R2를 실행하는 경우에는 이 화면이 표시되지 않습니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  **디스크 유형 선택** 페이지에서 가상 하드 디스크 유형을 **동적 확장**(권장)으로 설정합니다. **고정 크기** 디스크를 선택하면 작동은 되지만 오래 기다려야 할 수 있습니다. **차이점 보관용** 옵션은 사용하지 않는 것이 좋습니다. **다음**을 클릭합니다. **동적 확장**은 Hyper-V 2012 및 Hyper-V 2012 R2의 기본값입니다. Hyper-V 2008 R2의 기본값은 **고정 크기**입니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  **이름 및 위치 지정** 페이지에서 데이터 디스크의 **이름** 및 **위치**(해당 위치로 이동 가능)를 입력합니다. **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  **디스크 구성** 페이지에서 **비어 있는 새 가상 하드 디스크 만들기** 옵션을 선택하고 크기를 **500GB**(또는 그 이상)로 지정합니다. **다음**을 클릭합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  **요약** 페이지에서 가상 데이터 디스크의 세부 정보를 검토한 후 만족스러우면 **마침**을 클릭하여 디스크를 만듭니다. 마법사가 닫히고 가상 하드 디스크가 컴퓨터에 추가됩니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  **설정** 페이지로 돌아갑니다. **확인**을 클릭하여 **설정** 페이지를 닫고 Hyper-V 관리자 창으로 돌아갑니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## 3단계: 가상 장치를 시작하고 IP를 가져옵니다.

가상 장치를 시작하여 연결하려면 다음 단계를 수행합니다.

#### 가상 장치를 시작하려면

1.  가상 장치를 시작합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  장치가 실행된 후에 장치를 선택하고 마우스 오른쪽 단추를 클릭하여 **연결**을 선택합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  장치가 준비되기까지 5-10분 정도 걸릴 수 있습니다. 콘솔에 진행률을 나타내는 상태 메시지가 표시됩니다. 장치가 준비되면 **작업**으로 이동합니다. `Ctrl + Alt + Delete` 키를 눌러서 가상 장치에 로그인합니다. 기본 사용자는 *StorSimpleAdmin*이고 기본 암호는 *Password1*입니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  보안상의 이유로 장치 관리자 암호는 처음 로그인하면 만료됩니다. 암호를 변경하라는 메시지가 표시됩니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

	8자 이상을 포함하는 암호를 입력합니다. 암호는 4가지 요구 사항(소문자, 대문자, 숫자 및 특수 문자) 중 3가지 이상을 충족해야 합니다. 확인을 위해 암호를 다시 입력합니다. 암호가 변경되었다는 메시지가 표시됩니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  암호 변경이 완료되면 가상 장치가 다시 시작됩니다. 장치가 시작할 때까지 기다립니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

 	장치의 Windows PowerShell 콘솔이 진행률 표시줄과 함께 표시됩니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  6-8단계는 DHCP 환경이 아닌 곳에서 부팅하는 경우에만 적용됩니다. DHCP 환경인 경우에는 이 단계를 건너뛰고 9단계로 이동하세요. DHCP 환경이 아닌 곳에서 장치를 부팅하는 경우에는 다음 화면이 표시됩니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

 	이제 네트워크를 구성해야 합니다.

1.  `Get-HcsIpAddress` 명령을 사용하여 가상 장치에 사용하도록 설정된 네트워크 인터페이스 목록을 표시합니다. 장치에 사용하도록 설정된 네트워크 인터페이스가 하나인 경우에는 `Ethernet`이라는 기본 이름이 인터페이스에 할당됩니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  `Set-HcsIpAddress` cmdlet을 사용하여 네트워크를 구성합니다. 아래에 예가 나와 있습니다.

 	`Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

 	![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  초기 설정이 완료된 후 장치가 부팅되면 장치 배너 텍스트가 표시됩니다. 장치 관리를 위해 배너 텍스트에 표시되는 IP 주소와 URL을 기록해 둡니다. IP 주소를 사용하여 가상 장치의 웹 UI를 연결하고 로컬 설정 및 등록을 완료합니다.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (선택 사항) 이 단계는 정부 클라우드에서 장치를 배포하는 경우에만 수행합니다. 이제 장치에서 미국 FIPS(Federal Information Processing Standard) 모드를 사용할 수 있습니다. FIPS 140 표준은 중요한 데이터의 보호를 위해 미국 연방 정부 컴퓨터 시스템에서 사용할 수 있도록 승인된 암호화 알고리즘을 정의합니다.
	1. FIPS 모드를 사용하도록 설정하려면 다음 cmdlet을 실행합니다.
		
		`Enter-HcsFIPSMode`

	2. 암호화 유효성 검사에 적용되도록 FIPS 모드를 사용하도록 설정한 후 장치를 다시 부팅합니다.

		> [AZURE.NOTE] 장치에서 FIPS 모드를 사용 또는 사용하지 않도록 설정할 수 있습니다. 장치에서 FIPS 모드와 비 FIPS 모드가 교대로 반복되는 기능은 지원되지 않습니다.

장치가 최소 구성 요구 사항을 충족하지 못하면 배너 텍스트에 오류(아래 참고)가 표시됩니다. 최소 요구 사항을 충족하기에 충분한 리소스를 확보하도록 장치 구성을 수정해야 합니다. 그런 다음 다시 시작하고 장치에 연결합니다. [1단계: 호스트 시스템이 최소 가상 장치 요구 사항을 충족하도록 합니다.](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements)에서 최소 구성 요구 사항을 참조하세요.

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

로컬 웹 UI를 사용하여 초기 구성을 하는 동안 다른 오류가 발생하면 [로컬 웹 UI를 사용하여 StorSimple 가상 배열 관리](storsimple-ova-web-ui-admin.md)에서 다음 워크플로를 참조하세요.

-   진단 테스트를 실행하여 [웹 UI 설정 문제를 해결](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)합니다.

-   [로그 패키지를 생성하고 로그 파일을 살펴봅니다](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![동영상 아이콘](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **동영상 사용 가능**

Hyper-V에서 StorSimple 가상 배열을 프로비전하는 방법을 보려면 동영상을 시청하세요.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## 다음 단계

-   [StorSimple 가상 배열을 파일 서버로 설정](storsimple-ova-deploy3-fs-setup.md)

-   [StorSimple 가상 배열을 iSCSI 서버로 설정](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0316_2016-->