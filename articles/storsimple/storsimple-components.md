<properties 
   pageTitle="StorSimple 구성 요소는 무엇인가요?" 
   description="StorSimple 장치, 서비스 및 관리 기술을 설명합니다." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="06/11/2015"
   ms.author="v-sharos"/>


# StorSimple 구성 요소는 무엇인가요? 

온-프레미스 장치 및 Microsoft Azure 클라우드 저장소 간의 저장소 작업을 관리하는 통합된 저장소 솔루션인 Microsoft Azure StorSimple을 시작합니다. StorSimple은 저장소 비용을 줄이고, 저장소 관리를 간소화하고, 재해 복구 기능과 효율성을 개선하고 데이터 이동성을 제공하도록 설계되었습니다.

다음 섹션에서는 Microsoft Azure StorSimple 구성 요소에 대해 설명하고 솔루션이 데이터를 정렬하고, 저장소를 할당하고 저장소 관리 및 데이터 보호를 용이하게 방법에 대해 설명합니다.

> [AZURE.NOTE]Microsoft Azure 웹 사이트에 게시된 StorSimple 배포 정보는 StorSimple 8000 시리즈 장치에만 적용됩니다. 7000 시리즈 장치에 대한 정보는 [StorSimple 도움말](http://onlinehelp.storsimple.com/)을 참조하세요.

## StorSimple 장치

Microsoft Azure StorSimple 장치는 기본 저장소 및 그 곳에 저장된 데이터에 대한 iSCSI 액세스를 제공하는 온-프레미스 하이브리드 저장소 배열입니다. 클라우드 저장소와의 통신을 관리하고 보안 및 Microsoft Azure StorSimple 솔루션에 저장된 모든 데이터의 기밀성을 보장하는데 도움이 됩니다.

StorSimple 장치에 클러스터링 및 자동 장애 조치에 대한 지원 및 반도체 드라이브(Ssd) 및 하드 디스크 드라이브(Hdd)가 포함됩니다. 공유 프로세서, 공유 저장소 및 미러링된 컨트롤러 두 개를 포함합니다. 각 컨트롤러는 다음을 제공합니다.

- 호스트 컴퓨터에 연결
- 로컬 영역 네트워크(LAN)에 연결하는데 최대 6개의 네트워크 포트
- 하드웨어 모니터링
- 전원이 중단되는 경우 정보를 유지하는 비휘발성 임의 액세스 메모리(NVRAM)
- 클러스터 인식 업데이트는 서비스 가용성에 영향을 주지 않거나 최소한의 영향을 주도록 장애 조치 클러스터의 서버의 소프트웨어 업데이트를 관리합니다.
- 백엔드 클러스터와 같은 기능의 클러스터 서비스는 고가용성을 제공하며 HDD 또는 SSD가 고장나거나 오프라인으로 전환되는 경우 발생할 수 있는 부정적인 영향을 최소화합니다.

언제든지 컨트롤러 하나만 활성화됩니다. 활성 컨트롤러에 실패하면 두 번째 컨트롤러가 자동으로 활성화됩니다.

자세한 내용은 [StorSimple 장치](https://msdn.microsoft.com/library/azure/dn772363.aspx)를 참조하세요.

## StorSimple 가상 장치

StorSimple을 사용하여 실제 하이브리드 저장소 장치의 아키텍처 및 기능을 복제하는 가상 장비를 작성할 수 있습니다.

StorSimple 가상 장치(StorSimple 가상 어플라이언스라고 하는)는 Azure 가상 컴퓨터의 단일 노드에서 실행됩니다. (가상 장치는 Azure 가상 컴퓨터에만 만들 수 있습니다. StorSimple 장치 또는 온-프레미스 서버에 만들 수 없습니다.) StorSimple 가상 장치와 물리적 StorSimple 장치는 다음과 같이 다릅니다.

- 가상 장치는 인터페이스가 하나만 있는 반면 물리적 장치에는 6개의 네트워크 인터페이스가 있습니다. 
- 별도의 작업이 아닌 장치 구성 중 가상 장치를 등록합니다.
- 가상 장치에서 서비스 데이터 암호화 키를 다시 생성할 수 없습니다. 키 롤오버 중 물리적 장치에서 키를 다시 생성한 다음 새 키로 가상 장치를 업데이트합니다.
- 가상 장치에 업데이트를 적용해야 하는 경우 일부 작동 중단 시간이 발생할 수 있습니다. 물리적 StorSimple 장치에는 발생하지 않습니다.

테스트 및 소규모 파일럿 배포와 같은 물리적 장치를 사용할 수 없는 재해 복구 시나리오에 대한 StorSimple 가상 장치를 사용하는 것이 좋습니다.

자세한 내용은 [StorSimple 가상 장치](https://msdn.microsoft.com/library/azure/dn772390.aspx)를 참조하세요.


## 저장소 관리 기술
 
전용 StorSimple 장치 및 가상 장치 외에, Microsoft Azure StorSimple은 다음 소프트웨어 기술을 사용하여 데이터에 대한 빠른 데이터 액세스를 제공하고 저장소 사용량을 줄입니다.

- 자동 저장소 계층화 
- 씬 프로비저닝 
- 중복 제거 및 압축 

### 자동 저장소 계층화

Microsoft Azure StorSimple은 현재 사용량, 수명 및 다른 데이터에 대한 관계에 따라 논리적 계층의 데이터를 자동으로 정렬합니다. 가장 활성화된 데이터는 로컬로 저장되지만, 덜 활성화된 데이터 및 덜 비활성된 데이터는 자동으로 클라우드로 마이그레이션됩니다. 그림 1에서는 이 저장소 사용 방식을 보여 줍니다.
 
![StorSimple 저장소 계층](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**그림 1: StorSimple 저장소**

빠른 액세스를 설정하려면 StorSimple은 StorSimple 장치의 SSD에 사용량이 매우 많은 데이터(핫 데이터)를 저장합니다. 가끔씩 사용되는 데이터(웜 데이터)를 데이터 센터에 있는 서버 또는 장치의 Hdd에 저장합니다. 비활성 데이터, 백업 데이터 및 보존 및 보관 또는 규정 준수용으로 보유한 데이터를 클라우드로 이동합니다.

StorSimple는 데이터와 저장소 할당을 조정하여 사용량 패턴 변경으로 다시 지정합니다. 예를 들어 일부 정보는 시간이 지남에 따라 덜 활성화될 수 있습니다. 점진적으로 덜 활성화되면서 SSD에서 HDD로 마이그레이션된 다음 클라우드로 마이그레이션됩니다. 일부 데이터가 다시 활성화되면, 저장 장치에 다시 마이그레이션됩니다.

### 씬 프로비저닝

씬 프로비저닝은 사용 가능한 저장소가 실제 리소스를 초과하는 것처럼 표시하는 가상화 기술입니다. 충분한 저장소를 사전에 예약하는 대신 StorSimple는 씬 프로비저닝을 사용하여 현재 요구 사항에 맞게 충분한 공간을 할당합니다. 클라우드 저장소의 탄력적인 특징은 StorSimple가 변화 하는 요구에 맞게 클라우드 저장소를 늘리거나 줄일 수 있으므로 이 방법을 용이하게 합니다.

### 중복 제거 및 압축

Microsoft Azure StorSimple은 중복 제거 및 데이터 압축을 사용하여 저장소 요구 사항을 줄일 수 있습니다.

중복 제거는 저장된 데이터 집합에서 중복을 제거하여 저장된 데이터의 전체 크기를 줄일 수 있습니다. 정보가 변경되면 StorSimple은 변경되지 않은 데이터를 무시하고 변경 내용만 캡처합니다. 또한 StorSimple은 불필요한 정보를 식별하여 제거하여 저장된 데이터의 크기를 줄입니다.

## StorSimple용 Windows PowerShell

StorSimple용 Windows PowerShell은 Microsoft Azure StorSimple 서비스 만들기, 관리, 설정 및 StorSimple 장치 설정 및 모니터링에 사용할 수 있는 명령줄 인터페이스를 제공합니다. StorSimple 장치를 관리하기 위한 전용 cmdlet을 포함하는 Windows PowerShell 기반의 명령줄 인터페이스입니다. StorSimple용 Windows PowerShell에서 다음을 수행할 수 있는 기능이 있습니다.

- 장치를 등록합니다.
- 장치에서 네트워크 인터페이스를 구성합니다.
- 특정 형식의 업데이트를 설치합니다.
- 지원 세션에 액세스하여 장치 문제를 해결합니다.
- 장치 상태를 변경합니다.

직렬 콘솔(장치에 직접 연결되는 호스트 컴퓨터)에서 또는 Windows PowerShell 원격 기능을 사용하여 원격으로 StorSimple용 Windows PowerShell에 액세스할 수 있습니다. 초기 장치 등록과 같이 StorSimple용 일부 Windows PowerShell은 직렬 콘솔에서만 수행할 수 있습니다.

자세한 내용은 [StorSimple용 Windows PowerShell](https://msdn.microsoft.com/library/azure/dn772425.aspx)을 참조하세요.

## StorSimple 관리자 서비스

Microsoft Azure StorSimple는 데이터 센터와 클라우드 저장소를 집중 관리하기 위해 사용하는 웹 기반 사용자 인터페이스(StorSimple Manager 서비스)를 제공합니다. 다음 작업을 수행하는 데 StorSimple Manager 서비스를 사용할 수 있습니다.

- StorSimple 장치에 대한 시스템 설정을 구성합니다.
- StorSimple 장치에 대한 보안 설정을 구성하고 관리합니다.
- 클라우드 자격 증명 및 속성을 구성합니다.
- 서버에서 볼륨을 구성하고 관리합니다.
- 볼륨 그룹을 구성합니다.
- 데이터를 백업하고 복원합니다.
- 성능을 모니터링합니다.
- 시스템 설정을 검토하고 발생 가능한 문제를 식별합니다.

초기 설정 및 업데이트의 설치와 같은 시스템 작동 중단을 필요로 하는 작업을 제외한 모든 관리 작업을 수행하려면 StorSimple Manager 서비스를 사용할 수 있습니다.

자세한 내용은 [StorSimple 관리자 서비스](https://msdn.microsoft.com/library/azure/dn772396.aspx)를 참조하세요.

## StorSimple 스냅숏 관리자

StorSimple 스냅숏 관리자는 로컬 및 클라우드 데이터의 일관된 지정 시간 백업 복사본을 만드는 데 사용할 수 있는 Microsoft Management Console(MMC) 스냅인입니다. 스냅인은 Windows Server 기반 호스트에서 실행됩니다. StorSimple 스냅숏 관리자를 사용하여 다음을 수행할 수 있습니다.

- 볼륨을 구성, 백업 및 삭제합니다.
- 백업된 데이터가 응용 프로그램과 일관되도록 볼륨 그룹을 구성합니다.
- 데이터를 미리 결정된 일정에 따라 백업하고 지정된 위치(로컬 또는 클라우드에)에 저장되도록 백업 정책을 관리합니다.
- 볼륨 및 개별 파일을 복원합니다.

백업은 스냅숏으로 캡처되며, 마지막 스냅숏 백업 이후 변경 내용만 기록하고 전체 백업보다 훨씬 적은 저장소 공간을 필요로 합니다. 백업 일정을 만들 수도 있고 필요에 따라 즉시 백업할 수 있습니다. 또한 저장될 스냅숏의 크기를 제어하는 보존 정책을 설정하려면 StorSimple 스냅숏 관리자를 사용할 수 있습니다. 이후에 백업에서 데이터를 복원해야 하는 경우, StorSimple 스냅숏 관리자는 로컬 또는 클라우드 스냅숏의 카탈로그에서 선택하게 할 수 있습니다.

재해가 발생한 경우 또는 다른 이유로 데이터를 복원해야 하는 경우, StorSimple 스냅숏 관리자는 필요할 때 증분 방식으로 복원합니다. 파일을 복원하거나, 장비를 교체 하거나 다른 사이트로 이동하는 동안 시스템 전체를 종료하지 않아도 됩니다.

자세한 내용은 [StorSimple 스냅숏 관리자](https://msdn.microsoft.com/library/azure/dn772365.aspx)를 참조하세요.

## SharePoint용 StorSimple 어댑터

Microsoft Azure StorSimple은 StorSimple 저장소 및 데이터 보호 기능을 SharePoint 서버 팜으로 투명하게 확장하는 선택적 구성 요소인, SharePoint용 StorSimple 어댑터를 포함합니다. 어댑터는 Blob를 Microsoft Azure StorSimple 시스템에서 백업한 서버로 이동할 수 있도록 원격 Blob 저장소(RBS) 공급자 및 SQL Server RBS 기능과 함께 작동합니다. Microsoft Azure StorSimple은 사용량에 따라 BLOB 데이터를 로컬 또는 클라우드에 저장합니다.

SharePoint용 StorSimple 어댑터는 SharePoint 중앙 관리 포털 내에서 관리됩니다. 따라서 SharePoint 관리가 중앙 집중화된 상태로 유지되며 모든 저장소는 SharePoint 팜에 있는 것으로 나타납니다.

자세한 내용은 [SharePoint 용 StorSimple 어댑터](https://msdn.microsoft.com/library/azure/dn757737.aspx)를 참조하세요.


## 다음 단계

[StorSimple 릴리스 정보](https://msdn.microsoft.com/library/azure/dn772367.aspx)를 검토하세요.




 

<!---HONumber=July15_HO1-->