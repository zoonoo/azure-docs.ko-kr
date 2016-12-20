---
title: "StorSimple 장치(업데이트 1) 배포 | Microsoft Docs"
description: "StorSimple 업데이트 1 장치 및 서비스를 배포하기 위한 단계 및 모범 사례를 설명합니다."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: ac631d3c-3c53-4c9e-9e4a-5c61c0cd8167
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8a3f389250920dcdb49a5f88812d2e3b88bfd6c6


---
# <a name="deploy-your-on-premises-storsimple-device-update-1"></a>온-프레미스 StorSimple 장치(업데이트 1) 배포
> [!div class="op_single_selector"]
> * [업데이트 2](storsimple-deployment-walkthrough-u2.md)
> * [업데이트 1](storsimple-deployment-walkthrough-u1.md)
> * [GA 릴리스](storsimple-deployment-walkthrough.md)
> 
> 

## <a name="overview"></a>개요
Microsoft Azure StorSimple 장치 배포를 시작합니다. 이러한 배포 자습서는 StorSimple 8000 시리즈 업데이트 1.0에 적용됩니다. 이 자습서 시리즈에서는 StorSimple 장치를 구성하는 방법에 대해 설명하며 구성 검사 목록, 구성 필수 목록 및 자세한 구성 단계를 포함합니다.

이 자습서의 정보는 안전 주의 사항을 검토했으며, StorSimple 장치의 포장을 풀었고, 랙을 탑재했으며, 케이블에 연결되어 있다고 가정합니다. 여전히 이러한 작업을 수행해야 하는 경우 [안전 주의 사항](storsimple-safety.md)검토로 시작하세요. 장치 모델에 따라 다음 지침을 따라 개봉, 랙 탑재, 케이블 연결을 할 수 있습니다.

* [8100 개봉, 랙 탑재, 케이블 연결](storsimple-8100-hardware-installation.md)
* [8600 개봉, 랙 탑재, 케이블 연결](storsimple-8600-hardware-installation.md)

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 시작하기 전에 구성 검사 목록을 검토하는 것이 좋습니다. 배포 및 구성 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.

> [!NOTE]
> Microsoft Azure 웹 사이트에 게시된 StorSimple 배포 정보는 StorSimple 8000 시리즈 장치에만 적용됩니다. 5000 및 7000 시리즈 장치에 대한 자세한 내용은 [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)으로 이동합니다. 5000 및 7000 시리즈 배포 정보는 [StorSimple 시스템 빠른 시작 가이드](http://onlinehelp.storsimple.com/111_Appliance/)를 참조하세요.
> 
> 

## <a name="deployment-steps"></a>배포 단계
StorSimple 장치를 구성하여 StorSimple 관리자 서비스에 연결하려면 다음과 같은 필수 단계를 수행합니다. 필수 단계 외에 선택적 단계 및 배포하는 동안 필요할 수도 있는 절차가 있습니다. 단계별 배포 지침은 각 선택적 단계를 수행해야 하는 시기를 나타냅니다.

| 단계 | 설명 |
| --- | --- |
| **필수 조건** |향후 배포 준비 과정에서 완료해야 합니다. |
| 배포 구성 검사 목록. |이 검사 목록을 사용하여 배포 이전 및 배포하는 동안 정보를 수집하고 기록합니다. |
| 배포 필수 조건. |배포할 준비가 되어 있는 환경인지 유효성을 검사합니다. |
|  | |
| **단계별 배포** |프로덕션 환경에서 StorSimple 장치를 배포하려면 다음 단계가 필요합니다. |
| 1단계: 새 서비스를 만듭니다. |StorSimple 장치에 대한 클라우드 관리 및 저장소를 설정합니다. 다른 StorSimple 장치에 대해 기존 서비스가 있는 경우 이 단계를 건너뜁니다. |
| 2단계: 서비스 등록 키를 가져옵니다. |이 키를 사용하여 StorSimple 장치를 관리 서비스에 등록 및 연결합니다. |
| 3단계: StorSimple용 Windows PowerShell을 통해 장치를 구성 및 등록합니다. |장치를 네트워크에 연결하고 관리 서비스를 사용하여 Azure로 등록하여 설정을 완료합니다. |
| 4단계: 최소 장치 설정 완료</br>선택 사항: StorSimple 장치를 업데이트합니다. |관리 서비스를 사용하여 장치 설정을 완료하고 저장소를 제공할 수 있도록 설정합니다. |
| 5단계: 볼륨 컨테이너를 만듭니다. |볼륨을 프로비전할 컨테이너를 만듭니다. 볼륨 컨테이너에는 저장소 계정, 대역폭 및 그 안에 포함된 모든 볼륨에 대한 암호화 설정이 있습니다. |
| 6단계: 볼륨을 만듭니다. |서버에 대한 StorSimple 장치의 저장소 볼륨을 프로비전합니다. |
| 7단계: 볼륨을 탑재, 초기화 및 포맷합니다.</br>선택 사항: MPIO를 구성합니다. |서버를 장치에서 제공하는 iSCSI 저장소에 연결합니다. 필요에 따라 MPIO를 구성하여 서버가 링크, 네트워크 및 인터페이스 실패를 허용할 수 있도록 합니다. |
| 8단계: 백업을 수행합니다. |백업 정책을 설정하여 데이터 보호 |
|  | |
| **기타 절차** |솔루션을 배포하는 경우 이러한 절차를 참조해야 합니다. |
| 서비스에 대한 새 저장소 계정을 구성합니다. | |
| PuTTY를 사용하여 장치 직렬 콘솔에 연결합니다. | |
| Windows Server 호스트의 IQN을 가져옵니다. | |
| 수동 백업을 만듭니다. | |

## <a name="deployment-configuration-checklist"></a>배포 구성 검사 목록
다음 배포 구성 검사 목록에서는 StorSimple 장치에 소프트웨어를 구성하기 전 및 구성하는 동안 수집해야 하는 정보를 설명합니다. 이 정보의 일부를 미리 준비하면 사용자 환경에서 StorSimple 장치를 배포하는 과정을 간소화하는데 도움이 됩니다. 또한 이 검사 목록을 사용하여 장치를 배포하는 동안 구성 세부 정보를 기록합니다.

| 단계 | 매개 변수 | 세부 정보 | 값 |
| --- | --- | --- | --- |
| **장치 케이블 연결** |직렬 액세스 |초기 장치 구성 |예/아니요 |
|  | | | |
| **장치 구성 및 등록** |데이터 0 네트워크 설정 |Data 0 IP 주소:</br>서브넷 마스크:</br>게이트웨이:</br>기본 DNS 서버:</br>기본 NTP 서버:</br>웹 프록시 서버 IP/FQDN(선택 사항):</br>웹 프록시 포트: | |
| &nbsp; |장치 관리자 암호 |암호는 8~15자이며 소문자, 대문자, 숫자 및 특수 문자를 포함해야 합니다. | |
| &nbsp; |StorSimple 스냅숏 관리자 암호 |암호는 14 또는 15자이며 소문자, 대문자, 숫자 및 특수 문자를 포함해야 합니다. | |
| &nbsp; |서비스 등록 키 |Azure 클래식 포털에서 이 키가 생성됩니다. | |
| &nbsp; |서비스 데이터 암호화 키 |이 키는 장치가 StorSimple용 Windows PowerShell을 통해 관리 서비스와 등록될 때 생성됩니다. 이 키를 복사하고 안전한 위치에 저장합니다. | |
|  | | | |
| **최소 장치 설정 완료** |장치의 친숙한 이름 |장치에 대한 설명이 포함된 이름입니다. | |
| &nbsp; |표준 시간대 |장치는 모든 예약된 작업에 대해 이 표준 시간대를 사용합니다. | |
| &nbsp; |보조 DNS 서버 |필요한 구성입니다. | |
| &nbsp; |네트워크 인터페이스: 데이터 0 컨트롤러 고정 IP |이러한 IP는 인터넷에 라우팅 가능해야 합니다.</br>컨트롤러 0 고정 IP 주소:</br>컨트롤러 1 고정 IP 주소: | |
|  | | | |
| **추가 네트워크 인터페이스 설정** |네트워크 인터페이스: 데이터 1</br>iSCSI가 사용되도록 설정된 경우 게이트웨이를 구성하지 마세요. |용도: 클라우드/iSCSI/사용되지 않음</br>IP 주소:</br>서브넷 마스크:</br>게이트웨이: | |
| &nbsp; |네트워크 인터페이스: 데이터 2</br>iSCSI가 사용되도록 설정된 경우 게이트웨이를 구성하지 마세요. |용도: 클라우드/iSCSI/사용되지 않음</br>IP 주소:</br>서브넷 마스크:</br>게이트웨이: | |
| &nbsp; |네트워크 인터페이스: 데이터 3</br>iSCSI가 사용되도록 설정된 경우 게이트웨이를 구성하지 마세요. |용도: 클라우드/iSCSI/사용되지 않음</br>IP 주소:</br>서브넷 마스크:</br>게이트웨이: | |
| &nbsp; |네트워크 인터페이스: 데이터 4</br>iSCSI가 사용되도록 설정된 경우 게이트웨이를 구성하지 마세요. |용도: 클라우드/iSCSI/사용되지 않음</br>IP 주소:</br>서브넷 마스크:</br>게이트웨이: | |
| &nbsp; |네트워크 인터페이스: 데이터 5</br>iSCSI가 사용되도록 설정된 경우 게이트웨이를 구성하지 마세요. |용도: 클라우드/iSCSI/사용되지 않음</br>IP 주소:</br>서브넷 마스크:</br>게이트웨이: | |
|  | | | |
| **볼륨 컨테이너 만들기** |볼륨 컨테이너 이름: |컨테이너의 이름 | |
| &nbsp; |Azure 저장소 계정: |이 볼륨 컨테이너와 연결할 저장소 계정 이름 및 액세스 키 | |
| &nbsp; |클라우드 저장소 암호화 키: |각 컨테이너의 저장소에 대한 암호화 키 | |
|  | | | |
| **볼륨 만들기** |각 볼륨에 대한 세부 정보 |볼륨 이름: | |
| &nbsp; |&nbsp; |크기: | |
| &nbsp; |&nbsp; |사용 유형: | |
| &nbsp; |&nbsp; |ACR 이름: | |
| &nbsp; |&nbsp; |기본 백업 정책: | |
|  | | | |
| **볼륨 탑재, 초기화 및 포맷** |저장소에 연결된 각 호스트 서버에 대한 세부 정보 |Windows Server 이름: | |
| &nbsp; |&nbsp; |Windows Server IQN: | |
| &nbsp; |&nbsp; |Windows Server 볼륨 이름: | |
| &nbsp; |&nbsp; |NTFS 탑재 지점/드라이브 문자: | |

## <a name="deployment-prerequisites"></a>배포 필수 조건
다음 섹션에서는 StorSimple 관리자 서비스 및 StorSimple 장치에 대한 필수 조건에 대해 설명합니다.

### <a name="for-the-storsimple-manager-service"></a>StorSimple 관리자 서비스의 경우
시작하기 전에 다음 사항을 확인합니다.

* 액세스 자격 증명이 있는 Microsoft 계정이 있습니다.
* 액세스 자격 증명이 있는 Microsoft Azure 저장소 계정이 있습니다.
* 사용자의 Microsoft Azure 구독을 StorSimple Manager 서비스에 사용할 수 있습니다. 구독은 [기업 계약](https://azure.microsoft.com/pricing/enterprise-agreement/)을 통해 구매해야 합니다.
* PuTTY와 같은 터미널 에뮬레이션 소프트웨어에 액세스할 수 있습니다.

### <a name="for-the-device-in-the-datacenter"></a>데이터 센터에서 장치의 경우
장치를 구성하기 전에 다음 사항을 확인합니다.

* 다음에서 설명한 대로 장치가 완전히 개봉, 랙에 탑재되고 전원, 네트워크 및 직렬 액세스에 완전히 연결되었습니다.
  
  * [8100 장치 개봉, 랙 탑재, 케이블 연결](storsimple-8100-hardware-installation.md)
  * [8600 장치 개봉, 랙 탑재, 케이블 연결](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>데이터 센터에서 네트워크의 경우
시작하기 전에 다음 사항을 확인합니다.

* [StorSimple 장치에 대한 네트워킹 요구 사항](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)에서 설명한 대로 데이터 센터 방화벽에서 포트가 열려 있어 iSCSI 및 클라우드 트래픽을 허용합니다.

## <a name="step-by-step-deployment"></a>단계별 배포
다음 단계별 지침을 사용하여 데이터 센터에서 StorSimple 장치를 배포합니다.

## <a name="step-1-create-a-new-service"></a>1단계: 새 서비스 만들기
StorSimple 관리자 서비스는 여러 StorSimple 장치를 관리할 수 있습니다. StorSimple 관리자 서비스의 새 인스턴스를 만들려면 다음 단계를 수행합니다.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [!IMPORTANT]
> 서비스와 함께 저장소 계정을 자동으로 만들도록 설정하지 않은 경우, 서비스를 성공적으로 만든 후 하나 이상의 저장소 계정을 만들어야 합니다. 이 저장소 계정은 볼륨 컨테이너를 만들 때 사용됩니다.
> 
> * 저장소 계정을 자동으로 만들지 않은 경우 자세한 지침은 [서비스에 대한 새 저장소 계정 구성](#configure-a-new-storage-account-for-the-service) 을 참조하세요.
> * 저장소 계정을 자동으로 생성하도록 설정한 경우, [2단계: 서비스 등록 키 받기](#step-2-get-the-service-registration-key)로 이동합니다.
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>2단계: 서비스 등록 키 받기
StorSimple 관리자 서비스를 실행한 후에는 서비스 등록 키를 받아야 합니다. 이 키는 StorSimple 장치를 서비스에 등록 및 연결하는 데 사용됩니다.

Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>3단계: StorSimple용 Windows PowerShell을 통해 장치 구성 및 등록
다음 절차에서 설명한 대로 StorSimple 장치의 초기 설정을 완료하려면 StorSimple용 Windows PowerShell을 사용합니다. 이 단계를 완료하려면 터미널 에뮬레이션 소프트웨어를 사용해야 합니다. 자세한 내용은 [장치 직렬 콘솔 연결에 PuTTY 사용](#use-putty-to-connect-to-the-device-serial-console)을 참조하세요.

[!INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4-complete-minimum-device-setup"></a>4단계: 최소 장치 설정 완료
StorSimple 장치의 최소 장치 구성에는 다음 사항이 필요합니다.

* 보조 DNS 서버를 설정합니다.
* 하나 이상의 네트워크 인터페이스에서 iSCSI를 사용하도록 설정합니다.
* 두 컨트롤러에 고정된 IP 주소를 할당합니다.

최소 장치 설정을 완료하려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>5단계: 볼륨 컨테이너 만들기
볼륨 컨테이너에는 저장소 계정, 대역폭 및 그 안에 포함된 모든 볼륨에 대한 암호화 설정이 있습니다. StorSimple 장치에서 볼륨 프로비저닝을 시작하려면 볼륨 컨테이너를 만들어야 합니다.

볼륨 컨테이너를 만들려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>6단계: 볼륨 만들기
볼륨 컨테이너를 만든 후에 서버에 대한 StorSimple 장치에 저장소 볼륨을 프로비전할 수 있습니다. 볼륨을 만들려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

> [!IMPORTANT]
> StorSimple 관리자는 씬 프로비저닝된 볼륨만 만들 수 있습니다. 완전히 또는 부분적으로 프로비전된 볼륨을 만들 수 없습니다.
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>7단계: 볼륨 탑재, 초기화 및 포맷
다음 단계는 Windows Server 호스트에서 수행됩니다.

> [!IMPORTANT]
> * StorSimple 솔루션의 고가용성을 위해 iSCSI를 구성하기 전에 호스트 서버에 MPIO를 구성하는 것이 좋습니다(선택 사항). 호스트 서버에 MPIO를 구성하면 서버가 링크, 네트워크 또는 인터페이스 오류를 허용할 수 있습니다.
> * Windows Server 호스트에서 MPIO 및 iSCSI 설치 및 구성 지침은 [StorSimple 장치에 대한 MPIO 구성](storsimple-configure-mpio-windows-server.md)으로 이동합니다. StorSimple 볼륨을 탑재, 초기화 및 포맷하는 단계도 포함됩니다.
> * Linux 호스트에서 MPIO 및 iSCSI 설치 및 구성 지침은 [StorSimple Linux 호스트에 대한 MPIO 구성](storsimple-configure-mpio-on-linux.md)
> 
> 

MPIO를 구성하지 않으려는 경우 다음 단계를 수행하여 Windows Server 호스트에서 StorSimple 볼륨을 탑재, 초기화 및 포맷합니다.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>8단계: 백업 수행
백업은 볼륨의 지정 시간 보호 기능을 제공하며 복원 시간을 최소화하면서 복구 기능을 개선합니다. StorSimple 장치에서 두 유형(로컬 스냅숏 및 클라우드 스냅숏)의 백업을 수행할 수 있습니다. 이러한 각 유형의 백업은 **예약됨** 또는 **수동**이 될 수 있습니다.

예약된 백업을 만들려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

언제든지 수동 백업을 수행할 수 있습니다. 과정은 [수동 백업 만들기](#create-a-manual-backup)로 이동합니다.

## <a name="configure-a-new-storage-account-for-the-service"></a>서비스에 대한 새 저장소 계정 구성
서비스와 저장소 계정을 자동으로 생성하도록 설정하지 않은 경우에만 수행해야 하는 선택적 단계입니다. StorSimple 볼륨 컨테이너를 만들려면 Microsoft Azure 저장소 계정이 필요합니다.

다른 지역에 Azure 저장소 계정을 만들어야 하는 경우 단계별 지침은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md) 를 참조하세요.

**StorSimple 관리자 서비스** 페이지의 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>장치 직렬 콘솔 연결에 PuTTY 사용
StorSimple용 Windows PowerShell에 연결하려면 PuTTY와 같은 터미널 에뮬레이션 소프트웨어를 사용해야 합니다. 직렬 콘솔을 통해 직접 또는 원격 컴퓨터에서 텔넷 세션을 열어 장치에 액세스할 때 PuTTY를 사용할 수 있습니다.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>업데이트 검색 및 적용
장치 업데이트는 몇 시간이 걸릴 수 있습니다. 다음 단계를 수행하여 장치에서 업데이트를 검색 및 적용합니다.
<!--can take 1-4 hours-->

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>장치를 업데이트하려면
1. 장치 **빠른 시작** 페이지에서 **장치**를 클릭합니다. 물리적 장치를 선택하고 **유지 관리**를 클릭한 다음 **업데이트 검색**을 클릭합니다.  
2. 사용 가능한 업데이트를 검색하는 작업이 만들어집니다. 업데이트를 사용할 수 있는 경우 **업데이트 검색**이 **업데이트 설치**로 변경됩니다. **업데이트 설치**를 클릭합니다.
3. 업데이트 작업이 만들어집니다. **작업**으로 이동하여 업데이트 상태를 모니터링합니다.
   
   > [!NOTE]
   > 업데이트 작업이 시작되면 50%로 상태를 즉시 표시합니다. 업데이트 작업이 완료된 후에 상태를 100%로 변경합니다. 업데이트 프로세스에 대한 실시간 상태가 없습니다.
   > 
   > 
4. 장치가 성공적으로 업데이트된 후 데이터 2 및 데이터 3이 비활성화된 경우 데이터 2 및 데이터 3 네트워크 인터페이스를 사용하도록 설정합니다.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>Windows Server 호스트의 IQN 가져오기
Windows Server® 2012를 실행하는 Windows 호스트의 iSCSI 정규화된 이름(IQN)을 가져오려면 다음 단계를 수행합니다.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>수동 백업 만들기
StorSimple 장치에서 단일 볼륨에 대한 주문형 수동 백업을 만들려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="configure-mpio"></a>MPIO 구성
MPIO(다중 경로 I/O)는 선택적 기능이며 Windows Server에 기본적으로 설치되지 않습니다. 서버 관리자를 통해 기능으로 설치해야 합니다. MPIO 설치 지침은 [StorSimple 장치에 대한 MPIO 구성](storsimple-configure-mpio-windows-server.md)으로 이동합니다.

Linux 호스트에 연결된 StorSimple 도구에 대한 MPIO 설치 지침은 [Linux 호스트에 대한 MPIO 구성](storsimple-configure-mpio-on-linux.md)으로 이동합니다.

> [!NOTE]
> MPIO는 StorSimple 가상 장치에서 지원되지 않습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* [가상 장치](storsimple-virtual-device-u2.md)를 구성합니다.
* [StorSimple 관리자 서비스](storsimple-manager-service-administration.md) 를 사용하여 StorSimple 장치를 관리할 수 있습니다.




<!--HONumber=Dec16_HO2-->


