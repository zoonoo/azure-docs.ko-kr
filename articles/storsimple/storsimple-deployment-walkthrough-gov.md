<properties 
   pageTitle="정부 포털에서 온-프레미스 StorSimple 장치 배포"
   description="Azure 정부 포털에서 StorSimple 업데이트 1 장치 및 서비스를 배포하기 위한 단계 및 모범 사례입니다."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/12/2015"
   ms.author="v-sharos" />

# 정부 포털에서 온-프레미스 StorSimple 장치 배포

## 개요

Microsoft Azure StorSimple 장치 배포를 시작합니다.

이러한 배포 자습서는 Azure 정부 포털에서 업데이트 1 소프트웨어를 실행하는 StorSimple 8000 시리즈에 적용됩니다.

이 자습서 시리즈에서는 StorSimple 장치를 구성하는 방법에 대해 설명하며 사전 설치 검사 목록, 구성 필수 목록 및 자세한 구성 단계를 포함합니다.

> [AZURE.NOTE]Microsoft Azure 웹 사이트 및 MSDN Library에 게시된 StorSimple 배포 정보는 StorSimple 8000 시리즈 장치에만 적용됩니다. 7000 시리즈 장치에 대한 자세한 내용은 [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)으로 이동합니다. 7000 시리즈 배포 정보는 [StorSimple 시스템 퀵 스타트 가이드](http://onlinehelp.storsimple.com/111_Appliance/)를 참조하세요.

이 자습서의 정보는 안전 주의 사항 및 구성 검사 목록을 검토했으며, StorSimple 장치의 포장을 풀었고, 랙을 탑재했으며, 케이블에 연결되어 있다고 가정합니다. 아직 해당 작업을 수행해야 하는 경우 필요에 따라 [안전 주의 사항](https://msdn.microsoft.com/library/azure/dn772366.aspx), [구성 검사 목록](https://msdn.microsoft.com/library/azure/dn757787.aspx) 및 [장치의 하드웨어 설치](https://msdn.microsoft.com/library/azure/dn772375.aspx)로 이동합니다.

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 시작하기 전에 사전 설치 검사 목록을 검토하는 것이 좋습니다. 배포 및 구성 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.

## 사전 설치 검사 목록

다음 사전 설치 검사 목록에서는 StorSimple 장치에 소프트웨어를 구성하기 전에 수집해야 하는 정보를 설명합니다. 이 정보를 미리 준비하면 사용자 환경에서 StorSimple 장치를 배포하는 과정을 간소화 하는 데 도움이 됩니다.

| | 요구 사항 | 세부 정보 | 값 |
|---| --------------------- | ---------------------- | ------------- |
| 1 | 네트워크 설정 <ol><li>네트워크 인터페이스, 1GbE 4개, 10GbE 2개</li><li>고정 컨트롤러 IP</li><li>서브넷 마스크</li><li>게이트웨이</li></ol> | 필요한 IP는 총 8개로 <ol><li>사용할 수 있는 네트워크 인터페이스당 1개, 총 6개</li><li>컨트롤러당 1개, 총 2개</li><li>각 IP 주소당 1개</li><li>장치당 1개</li></ol>로 업데이트를 제공하기 위해 인터넷에 연결되어 있어야 합니다. | |
| 2 | 직렬 액세스 | 초기 장치 구성 | 예/아니요 |
| 3 | DNS 서버 IP 주소 | Microsoft Azure 연결에 필요한 수: 고가용성을 위해 총 2개 필요 | |
| 4 | NTP 서버 IP 주소 | Azure와 시간을 동기화 하는 데 필요한 수: 필수 1개, 선택 사항 1개 | |
| 5 | 프록시 서버(선택 사항) | IP 주소/프록시 서버의 정규화 된 도메인 이름, 사용할 포트 | |
| 6 | Azure 저장소 계정 | 계정 이름 및 액세스 키와 같은 액세스 자격 증명, 저장소 계정당 | |
| 7 | 클라우드 저장소 암호화 키(권장) | 볼륨 컨테이너당 | |
| 8 | 호스트의 IQN | 호스트당 | |

## 배포 필수 조건

다음 섹션에서는 StorSimple 관리자 서비스 및 StorSimple 장치에 대한 필수 조건에 대해 설명합니다.

### StorSimple 관리자 서비스의 경우

시작하기 전에 다음 사항을 확인합니다.

- 액세스 자격 증명이 있는 Microsoft 계정이 있습니다.

- 액세스 자격 증명이 있는 Microsoft Azure 저장소 계정이 있습니다.

- 사용자의 Microsoft Azure 구독을 StorSimple 관리자 서비스에 사용할 수 있습니다. 구독은 [기업 계약](http://azure.microsoft.com/pricing/enterprise-agreement/)을 통해 구매해야 합니다.

- PuTTY와 같은 터미널 에뮬레이션 소프트웨어에 액세스할 수 있습니다.

### 데이터 센터에서 장치의 경우

장치를 구성하기 전에 다음 사항을 확인합니다.

- [8100 장치 포장 풀기](https://msdn.microsoft.com/library/azure/dn772327.aspx) 또는 [8600 장치 포장 풀기](https://msdn.microsoft.com/library/azure/dn772418.aspx)에서 설명한 대로 장치의 포장을 완전히 풉니다.

- [8100 장치 랙 탑재](https://msdn.microsoft.com/library/azure/dn757749.aspx) 또는 [8600 장치 랩 탑재](https://msdn.microsoft.com/library/azure/dn757745.aspx)에서 설명한 대로 장치를 랙에 탑재합니다.

- [8100 장치 케이블 연결](https://msdn.microsoft.com/library/azure/dn757738.aspx) 또는 [8600 장치 케이블 연결](https://msdn.microsoft.com/library/azure/dn757762.aspx)에서 설명한 대로 장치를 전원, 네트워크 및 직렬 액세스에 완전히 연결합니다.

- [StorSimple 장치에 대한 네트워킹 요구 사항](https://msdn.microsoft.com/library/dn772371.aspx)에서 설명한 대로 데이터 센터 방화벽에서 포트가 열려 있어 iSCSI 및 클라우드 트래픽을 허용합니다.

## 배포 단계

StorSimple 장치를 구성하여 StorSimple 관리자 서비스에 연결하려면 다음과 같은 필수 단계를 수행합니다.

- 1단계: 새 서비스 만들기 
- 2단계: 서비스 등록 키 받기
- 3단계: StorSimple용 Windows PowerShell을 통해 장치 구성 및 등록 
- 4단계: 최소 장치 설정 완료
- 5단계: 볼륨 컨테이너 만들기 
- 6단계: 볼륨 만들기
- 7단계: 볼륨 탑재, 초기화 및 포맷 
- 8단계: 백업 수행

필수 단계 외에도 솔루션 배포에 따라 완료해야 할 수 있는 몇 가지 선택적 단계가 있습니다. 이러한 선택적 단계에서는 다음 사항에 대해 설명합니다.

- 서비스에 대한 새 저장소 계정 구성
- 장치 직렬 콘솔 연결에 PuTTY 사용
- Windows Server 호스트의 IQN 가져오기
- 수동 백업 만들기
- MPIO 구성

단계별 배포 지침은 각 선택적 단계를 수행해야 하는 시기를 나타냅니다.

## 1단계: 새 서비스 만들기

StorSimple 관리자 서비스는 여러 StorSimple 장치를 관리할 수 있습니다. StorSimple 관리자 서비스의 새 인스턴스를 만들려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [AZURE.IMPORTANT]서비스와 함께 저장소 계정을 자동으로 만들도록 설정하지 않은 경우, 서비스를 성공적으로 만든 후 하나 이상의 저장소 계정을 만들어야 합니다. 이 저장소 계정은 볼륨 컨테이너를 만들 때 사용됩니다.
>
> * 저장소 계정을 자동으로 만들지 않은 경우 자세한 지침은 [서비스에 대한 새 저장소 계정 구성](#configure-a-new-storage-account-for-the-service)을 참조하세요. 
> * 저장소 계정을 자동으로 생성하도록 설정한 경우, [2단계: 서비스 등록 키 받기](#step-2:-get-the-service-registration-key)로 이동합니다.

## 2단계: 서비스 등록 키 받기

StorSimple 관리자 서비스를 실행한 후에는 서비스 등록 키를 받아야 합니다. 이 키는 StorSimple 장치를 서비스에 등록 및 연결하는 데 사용됩니다.

정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]


## 3단계: StorSimple용 Windows PowerShell을 통해 장치 구성 및 등록

다음 절차에서 설명한 대로 StorSimple 장치의 초기 설정을 완료하려면 StorSimple용 Windows PowerShell을 사용합니다. 이 단계를 완료하려면 터미널 에뮬레이션 소프트웨어를 사용해야 합니다. 자세한 내용은 [장치 직렬 콘솔 연결에 PuTTY 사용](#use-putty-to-connect-to-the-device-serial-console)을 참조하세요.

[AZURE.INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

## 4단계: 최소 장치 설정 완료

StorSimple 장치의 최소 장치 구성에는 다음 사항이 필요합니다.

- 보조 DNS 서버를 설정합니다.
- 하나 이상의 네트워크 인터페이스에서 iSCSI를 사용하도록 설정합니다.
- 두 컨트롤러에 고정된 IP 주소를 할당합니다.

최소 장치 설정을 완료하려면 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## 5단계: 볼륨 컨테이너 만들기

볼륨 컨테이너에는 저장소 계정, 대역폭 및 그 안에 포함된 모든 볼륨에 대한 암호화 설정이 있습니다. StorSimple 장치에서 볼륨 프로비저닝을 시작하려면 볼륨 컨테이너를 만들어야 합니다.

볼륨 컨테이너를 만들려면 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## 6단계: 볼륨 만들기

볼륨 컨테이너를 만든 후에 서버에 대한 StorSimple 장치에 저장소 볼륨을 프로비전할 수 있습니다. 볼륨을 만들려면 정부 포털에서 다음 단계를 수행합니다.

> [AZURE.IMPORTANT]Azure StorSimple은 씬 프로비저닝된 볼륨만 만들 수 있습니다. Azure StorSimple 시스템에서 완전히 프로비전되거나 부분적으로 프로비전된 볼륨은 만들 수 없습니다.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## 7단계: 볼륨 탑재, 초기화 및 포맷

Windows Server 호스트에서 이러한 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## 8단계: 백업 수행

백업은 볼륨의 지정 시간 보호 기능을 제공하며 복원 시간을 최소화하면서 복구 기능을 개선합니다. StorSimple 장치에서 두 유형(로컬 스냅숏 및 클라우드 스냅숏)의 백업을 수행할 수 있습니다. 이러한 각 유형의 백업은 **예약됨** 또는 **수동**이 될 수 있습니다.

예약된 백업을 만들려면 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

언제든지 수동 백업을 수행할 수 있습니다. 과정은 [수동 백업 만들기](#create-a-manual-backup)로 이동합니다.

## 서비스에 대한 새 저장소 계정 구성

서비스와 저장소 계정을 자동으로 생성하도록 설정하지 않은 경우에만 수행해야 하는 선택적 단계입니다. StorSimple 볼륨 컨테이너를 만들려면 Microsoft Azure 저장소 계정이 필요합니다.

다른 지역에 Azure 저장소 계정을 만들어야 하는 경우 단계별 지침은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.

**StorSimple 관리자 서비스** 페이지의 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## PuTTY를 사용하여 장치 직렬 콘솔에 연결

StorSimple용 Windows PowerShell에 연결하려면 PuTTY와 같은 터미널 에뮬레이션 소프트웨어를 사용해야 합니다. 직렬 콘솔을 통해 직접 또는 원격 컴퓨터에서 텔넷 세션을 열어 장치에 액세스할 때 PuTTY를 사용할 수 있습니다.

[AZURE.INCLUDE [장치 직렬 콘솔 연결에 PuTTY 사용](../../includes/storsimple-use-putty.md)]

## Windows Server 호스트의 IQN 가져오기

Windows Server® 2012를 실행하는 Windows 호스트의 iSCSIㅋIQN(정규화된 iSCSI 이름)을 가져오려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [수동 백업 만들기](../../includes/storsimple-get-iqn.md)]

## 수동 백업 만들기

StorSimple 장치에서 단일 볼륨에 대한 주문형 수동 백업을 만들려면 정부 포털에서 다음 단계를 수행합니다.

[AZURE.INCLUDE [수동 백업 만들기](../../includes/storsimple-create-manual-backup-gov.md)]

## MPIO 구성

MPIO(다중 경로 I/O)는 선택적 기능이며 Windows Server에 기본적으로 설치되지 않습니다. 서버 관리자를 통해 기능으로 설치해야 합니다.

> [AZURE.NOTE]MPIO는 StorSimple 가상 장치에서 지원되지 않습니다.

MPIO 설치 지침은 [StorSimple 장치에 대한 MPIO 구성](storsimple-configure-mpio-windows-server.md)으로 이동합니다.

## 다음 단계

[가상 장치](storsimple-virtual-device.md)를 구성합니다.

[StorSimple 관리자 서비스](https://msdn.microsoft.com/library/azure/dn772396.aspx)를 사용하여 StorSimple 장치를 관리할 수 있습니다.
 

<!---HONumber=July15_HO4-->