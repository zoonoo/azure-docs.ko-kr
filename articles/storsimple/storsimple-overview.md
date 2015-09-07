<properties 
   pageTitle="StorSimple 정의 | Microsoft Azure"
	description="StorSimple 데이터 관리 및 보호 프로세스, 이점 및 아키텍처에 대해 설명하고 StorSimple 구성 요소를 소개합니다."
	services="storsimple"
	documentationCenter="NA"
	authors="SharS"
	manager="carolz"
	editor=""/>

<tags
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="08/26/2015"
	ms.author="v-sharos@microsoft.com"/>

# StorSimple 8000 시리즈: 하이브리드 클라우드 저장소 솔루션 

## 개요

Microsoft Azure StorSimple은 다양한 문제 및 엔터프라이즈 저장소 및 데이터 보호와 관련된 비용을 제거하는 효과적이고, 비용면에서 효율적인 관리 솔루션입니다. 소유 장치(Microsoft Azure StorSimple 장치), 클라우드 서비스와의 통합 및 관리 도구 집합을 사용하여 클라우드 저장소를 비롯한 모든 엔터프라이즈 저장소의 원활한 뷰를 제공합니다.

StorSimple은 저장소 계층화를 사용하여 다양한 저장소 미디어에 저장된 데이터를 관리합니다. 현재 작업 집합은 SSD(반도체 드라이브)에 온-프레미스로 저장되고, 자주 사용하지 않는 데이터는 HDD(하드 디스크 드라이브)에 저장되고, 보관 데이터는 클라우드에 푸시됩니다. 또한 StorSimple은 중복 제거 및 압축을 사용하여 데이터가 사용하는 저장소 크기를 줄입니다. 저장소 계층화 프로세스는 다음과 같이 수행됩니다.

1. 시스템 관리자가 Microsoft Azure 클라우드 저장소 계정을 설정합니다.
2. 관리자는 직렬 콘솔과 Azure 관리 포털에서 실행되는 StorSimple Manager 서비스를 사용하여 장치 및 파일 서버를 구성하고 볼륨 및 데이터 보호 정책을 만듭니다. 온-프레미스 파일 서버가 iSCSI(Internet Small Computer System Interface)를 사용하여 StorSimple 장치에 액세스합니다.
3. 처음에 StorSimple은 장치의 빠른 SSD 계층에 데이터를 저장합니다.
4. SSD 계층이 용량에 가까워지면 StorSimple에서 가장 오래된 데이터 블록을 중복 제거 및 압축하고 HDD 계층으로 이동합니다.
5. HDD 계층이 용량에 가까워지면 StorSimple에서 가장 오래된 데이터 블록을 암호화하고 HTTPS를 통해 Microsoft Azure 저장소 계정으로 안전하게 보냅니다.
6. Microsoft Azure가 해당 데이터 센터 및 원격 데이터 센터에 데이터의 여러 복제본을 만들어 재해가 발생할 경우 데이터를 복구할 수 있도록 합니다. 
7. 파일 서버가 클라우드에 저장된 데이터를 요청하는 경우 StorSimple에서 원활하게 데이터를 반환하고 StorSimple 장치의 SSD 계층에 복사본을 저장합니다.

저장소 관리 외에도 StorSimple 데이터 보호 기능을 통해 주문형 백업 및 예약된 백업을 만들고 로컬로 또는 클라우드에 저장할 수 있습니다. 백업은 증분 스냅숏 형태로 생성되므로 빠르게 만들고 복원할 수 있습니다. 클라우드 스냅숏은 보조 저장소 시스템(예: 테이프 백업)을 대체하기 때문에 재해 복구 시나리오에서 매우 중요할 수 있으며, 필요한 경우 데이터 센터 또는 대체 사이트에 데이터를 복원할 수 있게 해줍니다.

>[AZURE.NOTE]소프트웨어 업데이트 1 이상이 설치된 StorSimple 8000 시리즈는 RRS, HP 및 OpenStack 클라우드 서비스와 Microsoft Azure에서 Amazon S3를 지원합니다. 장치 관리를 위해 여전히 Microsoft Azure 저장소 계정이 필요합니다. 자세한 내용은 [서비스에 대한 새 저장소 계정 구성](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service)을 참조하세요.

## StorSimple을 사용하는 이유

Microsoft Azure StorSimple은 다음과 같은 이점을 제공합니다.

- **투명한 통합** – Microsoft Azure StorSimple은 iSCSI 프로토콜을 사용하여 데이터 저장소 시설을 보이지 않게 연결합니다. 이 클라우드, 데이터 센터 또는 원격 서버에 저장된 데이터가 단일 위치에 저장된 것처럼 보이게 합니다.
- **저장소 비용 감소** – Microsoft Azure StorSimple은 현재 요구에 맞게 충분한 로컬 또는 클라우드 저장소를 할당하고 필요한 경우 클라우드 저장소를 확장합니다. 동일한 데이터의 중복 버전을 제거하고(중복 제거) 압축을 사용하여 추가로 저장소 요구 사항 및 비용을 줄입니다.
- **단순화된 저장소 관리** – Microsoft Azure StorSimple은 온-프레미스, 원격 서버와 클라우드에 저장된 데이터를 구성 및 관리하는 데 사용할 수 있는 시스템 관리 도구를 제공합니다. 또한 Microsoft Management Console(MMC) 스냅인에서 백업 및 복원 기능을 관리할 수 있습니다. StorSimple은 StorSimple 관리 및 데이터 보호 서비스를 SharePoint 서버에 저장된 콘텐츠까지 확장하는 데 사용할 수 있는 별도의 선택적 인터페이스를 제공합니다. 
- **향상된 재해 복구 및 규정 준수** – Microsoft Azure StorSimple은 복구 시간을 연장하지 않아도 됩니다. 대신, 필요할 때마다 데이터를 복원합니다. 즉, 정상 작업 중단을 최소화하면서 계속할 수 있습니다. 또한 백업 일정 및 데이터 보존 기간을 지정하는 정책을 구성할 수 있습니다.
- **데이터 이동성** – 복구 및 마이그레이션을 위해 다른 사이트에서 Microsoft Azure 클라우드 서비스에 업로드된 데이터에 액세스할 수 있습니다. 또한 Microsoft Azure에서 실행 중인 가상 컴퓨터(Vm)에서 StorSimple 가상 장치를 구성하도록 StorSimple을 사용할 수 있습니다. VM을 가상 장치 사용하여 테스트 또는 복구 목적으로 저장된 데이터에 액세스할 수 있습니다. 

다음 다이어그램은 Microsoft Azure StorSimple 솔루션에 대한 상위 레벨 보기를 제공합니다.

![StorSimple 아키텍처](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Microsoft Azure StorSimple 아키텍처**

## StorSimple 구성 요소

Microsoft Azure StorSimple 솔루션에는 다음 구성 요소가 포함됩니다.

- **Microsoft Azure StorSimple 장치** – SSD(반도체 드라이브) 및 HDD(하드 디스크 드라이브)가 포함된 온-프레미스 하이브리드 저장소 배열로, 중복 컨트롤러 및 자동 장애 조치 기능을 함께 제공합니다. 컨트롤러는 현재 사용되는(또는 핫) 데이터를 로컬 저장소(장치 또는 온-프레미스 서버)에 배치하고 덜 자주 사용되는 데이터는 클라우드로 이동하여 저장소 계층화를 관리합니다.
- **StorSimple 가상 장치** – StorSimple 가상 어플라이언스라고도 하며, 물리적 하이브리드 저장 장치의 아키텍처와 기능을 복제하는 StorSimple 장치의 소프트웨어 버전입니다. StorSimple 가상 장치는 Azure 가상 컴퓨터의 단일 노드에서 실행됩니다. 가상 장치는 테스트 및 소규모 파일럿 시나리오에 사용하기에 적합합니다. 온-프레미스 서버나 StorSimple 장치에서 StorSimple 가상 장치를 만들 수 없습니다.
- **StorSimple용 Windows PowerShell** – StorSimple 장치를 관리하는 데 사용할 수 있는 명령줄 인터페이스입니다. StorSimple용 Windows PowerShell에는 StorSimple 장치를 등록하고, 장치에서 네트워크 인터페이스를 구성하고 특정 형식의 업데이트를 설치하고, 지원 세션에 액세스하여 장치 문제를 해결하고 장치 상태를 변경할 수 있는 기능이 있습니다. 직렬 콘솔에 연결하거나 Windows PowerShell 원격 기능을 사용하여 StorSimple용 Windows PowerShell에 액세스할 수 있습니다.
- **Azure PowerShell StorSimple cmdlet** – 명령줄에서 서비스 수준 작업 및 마이그레이션 작업을 자동화할 수 있도록 하는 Windows PowerShell cmdlet 모음입니다. StorSimple용 Azure PowerShell cmdlet에 대한 자세한 내용은 [cmdlet 참조](https://msdn.microsoft.com/library/dn920427.aspx)를 참조하세요.
- **StorSimple Manager 서비스** – 단일 웹 인터페이스에서 StorSimple 장치 또는 StorSimple 가상 장치를 관리할 수 있는 Azure 관리 포털의 확장입니다. StorSimple Manager 서비스를 사용하여 서비스를 만들고 관리하고, 장치를 보고 관리하고, 경고를 보고, 볼륨을 관리하고 백업 정책 및 백업 카탈로그를 보고 관리할 수 있습니다.
- **StorSimple 스냅숏 관리자** – 볼륨 그룹 및 Windows 볼륨 섀도 복사본 서비스를 사용하여 응용 프로그램에 일관된 백업을 생성하는 MMC 스냅인입니다. 또한 백업 일정 및 복제를 만들거나 볼륨을 복원하려면 StorSimple 스냅숏 관리자를 사용할 수 있습니다. 
- **SharePoint용 StorSimple 어댑터** – SharePoint 관리 포털에서 StorSimple 저장소를 볼 수 있고 관리할 수 있도록 하면서 SharePoint 서버 팜으로 Microsoft Azure StorSimple 저장소 및 데이터 보호를 투명하게 확장하는 도구입니다.

## 다음 단계

[StorSimple](https://azure.microsoft.com/documentation/services/storsimple/)에 대해 자세히 알아봅니다.

[StorSimple 구성 요소 및 용어](storsimple-components.md)에 대해 자세히 알아봅니다.


 

<!---HONumber=August15_HO9-->