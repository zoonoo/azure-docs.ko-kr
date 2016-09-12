<properties
	pageTitle="Azure Site Recovery를 사용하여 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제(레거시) | Microsoft Azure" 
	description="클래식 포털의 레거시 배포에서 Azure Site Recovery를 사용하여 온-프레미스 VM 및 Windows/Linux 물리적 서버를 Azure에 복제하는 방법을 설명합니다." 
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/22/2016"
	ms.author="raynew"/>

# 클래식 포털을 사용하여 Azure Site Recovery를 통해 Azure에 VMware 가상 컴퓨터 및 물리적 서버 복제(레거시)

> [AZURE.SELECTOR]
- [Azure 포털](site-recovery-vmware-to-azure.md)
- [클래식 포털](site-recovery-vmware-to-azure-classic.md)
- [클래식 포털(레거시)](site-recovery-vmware-to-azure-classic-legacy.md)


Azure Site Recovery에 오신 것을 환영합니다! 이 문서는 클래식 포털에서 Azure Site Recovery를 사용하여 Azure에 온-프레미스 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 복제하기 위한 레거시 배포를 설명합니다.

## 개요

조직에서는 계획된 중단 또는 불의의 중지 시간에 앱, 워크로드 및 데이터를 실행 중이고 가용 상태로 유지하고 가능한 신속히 정상적인 작업 상태로 복귀하기 위한 BCDR 전략이 필요합니다. BCDR 전략은 재해가 발생했을 때 비즈니스 데이터를 안전하고 복구 가능하게 유지하고 워크로드를 지속적으로 가용 상태로 유지해야 합니다.

사이트 복구는 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터센터에 복제하는 것을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다. [Azure Site Recovery란?](site-recovery-overview.md)에서 자세한 내용을 확인해 보세요.


>[AZURE.WARNING] 이 문서에는 **레거시 지침**이 포함되어 있습니다. 새 배포에는 사용하지 마십시오. 대신 [이 지침에 따라](site-recovery-vmware-to-azure.md) Azure 포털에서 사이트 복구를 배포하거나, [이 지침을 사용하여](site-recovery-vmware-to-azure-classic.md) 클래식 포털에서 향상된 배포를 구성하세요. 이 문서에 설명된 방법을 사용하여 이미 배포한 경우에는, 클래식 포털에서 향상된 배포로 마이그레이션하는 것이 좋습니다.


## 향상된 배포로 마이그레이션

이 섹션은 이 문서의 지침을 사용하여 사이트 복구를 이미 배포한 경우에만 적절합니다.

기존 배포를 마이그레이션하려면 다음을 수행해야 합니다.

1. 온-프레미스 사이트에서 새 사이트 복구 구성 요소를 배포합니다.
2. 새 구성 서버에서 VMware VM의 자동 검색에 대한 자격 증명을 구성합니다.
3. 새 구성 서버로 VMware 서버를 검색합니다.
3. 새 구성 서버로 새 보호 그룹을 만듭니다.


시작하기 전에 다음을 수행합니다.

- 마이그레이션을 위해 유지 관리 기간을 계획하는 것이 좋습니다.
- **컴퓨터 마이그레이션** 옵션은 레거시 배포 중에 만들어진 기존 보호 그룹이 있는 경우에만 사용할 수 있습니다.
- 마이그레이션 단계를 완료한 후 15분 이상이 지나야 자격 증명이 새로 고침되며 가상 컴퓨터를 검색 및 새로 고침하여 보호 그룹에 추가할 수 있게 됩니다. 기다리지 않고 직접 새로 고침할 수도 있습니다.

마이그레이션 과정은 다음과 같습니다.

1. [클래식 포털에서 향상된 배포](site-recovery-vmware-to-azure-classic.md#enhanced-deployment)에 대해 읽어보세요. 향상된 [아키텍처](site-recovery-vmware-to-azure-classic.md#scenario-architecture), 및 [필수 조건](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)을 검토하세요.
2. 현재 복제하는 컴퓨터에서 모바일 서비스를 제거합니다. 컴퓨터를 새 보호 그룹에 추가하면 새 버전의 서비스가 해당 컴퓨터에 설치됩니다.
3. [자격 증명 모음 등록 키](site-recovery-vmware-to-azure-classic.md#step-4-download-a-vault-registration-key)를 다운로드하고 [통합 설치 마법사를 실행](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)하여 구성 서버, 프로세스 서버, 및 마스터 대상 서버 구성 요소를 설치합니다. [용량 계획](site-recovery-vmware-to-azure-classic.md#capacity-planning)에 대해 자세히 알아보세요.
4. 사이트 복구에서 VMware VM을 자동으로 검색하기 위해 VMware 서버에 액세스 하는 데 사용할 수 있는 [자격 증명을 설정](site-recovery-vmware-to-azure-classic.md#step-6-set-up-credentials-for-the-vcenter-server)합니다. [필요한 권한](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)에 대해 알아보세요.
5. [vCenter 서버 또는 vSphere 호스트](site-recovery-vmware-to-azure-classic.md#step-7-add-vcenter-servers-and-esxi-hosts)를 추가합니다. 서버가 사이트 복구 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.
6. [새 보호 그룹](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group)을 만듭니다. 포털을 새로 고침하여 가상 컴퓨터가 검색 및 표시될 때까지는 최대 15분까지 소요될 수 있습니다. 기다리지 않으려면 관리 서버 이름(클릭 안 함) > **새로 고침**을 강조 표시하면 됩니다.
7. 새 보호 그룹에서 **컴퓨터 마이그레이션**을 클릭합니다.

	![계정 추가](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)

8. **컴퓨터 선택**에서 마이그레이션 원본 보호 그룹과 마이그레이션할 컴퓨터를 선택합니다.

	![계정 추가](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)

9. **대상 설정 구성**에서 모든 컴퓨터에 대해 동일한 설정을 사용할지 여부를 지정하고 프로세스 서버와 Azure 저장소 계정을 선택합니다. 별도의 프로세스 서버가 없는 경우에는, 구성 서버의 IP 주소가 여기에 해당됩니다.


	![계정 추가](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

	> [AZURE.NOTE] [Migration of storage accounts](../resource-group-move-resources.md) 작업은 Site Recovery를 배포하는 데 사용되는 저장소 계정에서 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 수행할 수 없습니다.

10. **계정 지정**에서 프로세스 서버가 새 버전의 모바일 서비스를 푸시하기 위해 컴퓨터에 액세스하는 데 사용하도록 만든 계정을 선택합니다.

	![계정 추가](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)

11. 사이트 복구는 사용자가 제공한 Azure 저장소 계정에 복제된 데이터를 마이그레이션합니다. 필요에 따라 레거시 배포에 사용한 저장소 계정을 다시 사용할 수 있습니다.
12. 작업이 완료되면 가상 컴퓨터가 자동으로 동기화됩니다. 동기화가 완료된 후에 레거시 보호 그룹에서 가상 컴퓨터를 삭제할 수 있습니다.
13. 모든 컴퓨터를 마이그레이션한 후에 레거시 보호 그룹을 삭제할 수 있습니다.
14. 컴퓨터에 대한 장애 조치 속성 및 동기화가 완료된 후 Azure 네트워크 설정을 지정해야 합니다.
15. 기존 복구 계획이 있는 경우 **복구 계획 마이그레이션** 옵션을 사용하여 해당 계획을 향상된 배포로 마이그레이션할 수 있습니다. 모든 보호된 컴퓨터를 마이그레이션한 후에 이 작업을 수행해야 합니다.

	![계정 추가](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

>[AZURE.NOTE] 마이그레이션을 마친 후 [향상된 문서](site-recovery-vmware-to-azure-classic.md)를 계속 진행합니다. 이 레거시 문서의 나머지 부분은 더 이상 관련이 없으며 거기**에 설명된 단계를 더 이상 따를 필요가 없습니다.




## 무엇이 필요하나요?

이 다이어그램은 배포 구성 요소를 보여 줍니다.

![새 자격 증명 모음](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

다음 항목이 필요합니다.

**구성 요소** | **배포웹사이트를** | **세부 정보**
--- | --- | ---
**구성 서버** | Site Recovery와 동일한 구독에 있는 Azure 표준 A3 가상 컴퓨터. | 구독 서버는 Azure에서 보호된 컴퓨터, 프로세스 서버 및 마스터 대상 서버 간의 통신을 조정합니다. 복제를 설정하고 장애 조치(Failover) 발생 시 Azure에서 복구를 조정합니다.
**마스터 대상 서버** | Azure 가상 컴퓨터 - Windows Server 2012 R2 갤러리 이미지(Windows 컴퓨터 보호를 위한)를 기반으로 하는 Windows 서버 또는 Linux 컴퓨터 보호를 위한 OpenLogic CentOS 6.6 갤러리 이미지를 기반으로 하는 Linux 서버.<br/><br/> 세 가지 크기 옵션(표준 A4, 표준 D14 및 표준 DS4)을 사용할 수 있습니다.<br/><br/> 서버는 구성 서버와 동일한 Azure 네트워크에 연결됩니다.<br/><br/> 사이트 복구 포털에서 설정 | Azure 저장소 계정으로 Blob 저장소에 만든 연결된 VHD를 사용하여 보호된 컴퓨터의 복제된 데이터를 수신 및 유지합니다.<br/><br/> 특히, 프리미엄 저장소 계정을 사용하여 일관된 고성능과 짧은 대기 시간이 요구되는 워크로드에 대한 보호를 구성하는 경우 표준 DS4를 선택합니다.
**프로세스 서버** | Windows Server 2012 R2를 실행하는 온-프레미스 가상 또는 물리적 서버<br/><br/>보호할 컴퓨터와 동일한 네트워크 및 LAN 세그먼트에 배치하는 것이 좋지만 보호된 컴퓨터에서 L3 네트워크를 볼 수 있다면 다른 네트워크에서 실행할 수도 있습니다.<br/><br/> 사이트 복구 포털에서 설정하고 구성 서버에 등록합니다. | 보호된 컴퓨터가 복제 데이터를 온-프레미스 프로세스 서버로 보냅니다. 프로세스 서버에는 수신한 복제 데이터를 캐시할 디스크 기반 캐시가 있습니다. 해당 데이터에 대해 다양한 작업을 수행합니다.<br/><br/> 마스터 대상 서버에 보내기 전에 캐싱, 압축, 암호화를 통해 데이터를 최적화합니다.<br/><br/> 모바일 서비스의 푸시 설치를 처리합니다.<br/><br/> VMware 가상 컴퓨터의 자동 검색을 수행합니다.
**온-프레미스 컴퓨터** | Windows 또는 Linux를 실행하는 온-프레미스 VMware 가상 컴퓨터 또는 물리적 서버. | 하나 이상의 컴퓨터에 적용되는 복제 설정을 구성합니다. 개별 컴퓨터 또는 보다 일반적으로 복구 계획에 한데 모아놓은 여러 대의 컴퓨터를 장애 조치(Failover)할 수 있습니다. 
**모바일 서비스** | 보호할 각각의 가상 컴퓨터 또는 실제 서버에 설치됨<br/><br/> 수동으로 설치할 수도 있고, 컴퓨터에 대해 복제를 사용하도록 설정한 경우 프로세스 서버를 통해 자동으로 푸시 및 설치할 수도 있습니다. | 모바일 서비스는 초기 복제(다시 동기화)를 진행하는 동안 프로세스 서버에 데이터를 보냅니다. 컴퓨터가 보호된 상태가 된 후에(재동기화를 마친 후), 모바일 서비스는 메모리 내 디스크에 대한 쓰기를 캡처하여 프로세스 서버에 보냅니다. Windows 서버에 대한 응용 프로그램 일관성은 VSS를 사용하여 구현됩니다.
**Azure Site Recovery 자격 증명 모음** | Azure 구독을 통해 Site Recovery 자격 증명 모음을 만들고 자격 증명 모음에 서버를 등록합니다. | 자격 증명 모음은 온-프레미스 사이트와 Azure 간의 데이터 복제본, 장애 조치(Failover) 및 복구를 조정하고 오케스트레이션합니다.
**복제 메커니즘** | **인터넷을 통해** - 인터넷을 통해 안전한 SSL/TLS 채널을 사용하여 보호된 온-프레미스 서버와 통신하고 해당 서버의 데이터를 Azure에 복제합니다. 기본 옵션입니다.<br/><br/> **VPN/Express 경로** - VPN 연결을 통해 온-프레미스 서버와 Azure 간에 데이터를 전달 및 복제합니다. 온-프레미스 사이트와 Azure 네트워크 사이에 사이트 간 VPN 또는 Express 경로 연결을 설정해야 합니다.<br/><br/> 사이트 복제 배포 중에 복제를 수행할 방식을 선택합니다. 메커니즘을 구성한 후에는 기존 컴퓨터의 복제에 영향을 미치지 않고 메커니즘을 변경할 수 없습니다. | 두 옵션 모두 보호된 컴퓨터에 인바운드 네트워크 포트를 열 필요가 없습니다. 모든 네트워크 통신은 온-프레미스 사이트에서 시작됩니다. 

## 용량 계획

주요 고려 사항은 다음과 같습니다.

- **소스 환경** - VMware 인프라, 원본 컴퓨터 설정 및 요구 사항
- **구성 요소 서버** - 프로세스 서버, 구성 서버, 마스터 대상 서버

### 원본 환경 고려 사항

- **최대 디스크 크기** - 현재 가상 컴퓨터에 연결할 수 있는 최대 디스크 크기는 1TB입니다. 따라서 복제할 수 있는 원본 디스크의 최대 크기도 1TB로 제한됩니다.
- **원본당 최대 크기** - 단일 원본 컴퓨터의 최대 크기는 마스터 대상 서버에 대해 D14 인스턴스를 프로비저닝할 경우 31TB(31개 디스크 사용)입니다.
- **마스터 대상 서버당 원본 수** - 단일 마스터 대상 서버로 여러 원본 컴퓨터를 보호할 수 있습니다. 하지만, 디스크 복제 시 디스크 크기를 미러링하는 VHD가 Azure Blob 저장소에 만들어지고 마스터 대상 서버에 데이터 디스크로 연결되기 때문에 여러 마스터 대상 서버에서 단일 컴퓨터를 보호할 수 없습니다.
- **원본당 일일 최대 변경률** - 원본당 권장 변경률을 고려할 때 세 가지 요소를 고려해야 합니다. 대상에서 고려할 사항은 원본의 각 작업용 대상 디스크에 2개의 IOPS가 필요하다는 점입니다. 그 이유는 오래된 데이터 읽기와 새 데이터 쓰기가 대상 디스크에서 발생하기 때문입니다.
	- **프로세스 서버에서 지원하는 일일 변경률** - 하나의 원본 컴퓨터를 여러 프로세스 서버로 확장할 수 없습니다. 단일 프로세스 서버는 최대 1TB의 일일 변경률을 지원합니다. 따라서 1TB는 원본 컴퓨터에 대해 지원되는 최대 일일 데이터 변경률입니다.
	- **대상 디스크에서 지원하는 최대 처리량** - 원본 디스크당 최대 변동은 1일 144GB를 초과할 수 없습니다(8K 쓰기 크기). 다양한 쓰기 크기에 대한 대상의 처리량 및 IOPS를 확인하려면 마스터 대상 섹션의 표를 참조하세요. 이각 원본 IOP는 대상 디스크에 2개의 IOPS를 생성하므로 이 숫자를 2로 나누어야 합니다. 프리미엄 저장소 계정에 대한 대상을 구성하는 경우 [Azure 확장성 및 성능 목표](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts)를 참조하세요.
	- **저장소 계정에서 지원하는 최대 처리량** - 하나의 원본을 복수 저장소 계정으로 확장할 수 없습니다. 저장소 계정이 초당 최대 2만 개의 요청을 수신하고 각 원본 IOP가 대상 서버에 2개의 IOPS를 생성할 경우 원본 전체의 IOPS 수를 1만으로 유지하는 것이 좋습니다. 프리미엄 저장소 계정에 대한 원본을 구성하는 경우 [Azure 확장성 및 성능 목표](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts)를 참조하세요.

### 구성 요소 서버 고려 사항

표 1에 구성 및 마스터 대상 서버의 가상 컴퓨터 크기가 요약되어 있습니다.

**구성 요소** | **Azure 인스턴스 배포** | **코어 수** | **메모리** | **최대 디스크 수** | **디스크 크기**
--- | --- | --- | --- | --- | ---
구성 서버 | 표준 A3 | 4 | 7 GB | 8 | 1023GB
마스터 대상 서버 | 표준 A4 | 8 | 14 GB | 16 | 1023GB
 | 표준 D14 | 16 | 112GB | 32 | 1023GB
 | 표준 DS4 | 8 | 28GB | 16 | 1023GB

**표 1**

#### 프로세스 서버 고려 사항

일반적으로 프로세스 서버 크기 조정은 보호된 모든 워크로드에서 일일 변경률에 따라 다릅니다.


- 인라인 압축, 암호화와 같은 작업을 수행하려면 충분한 컴퓨팅이 필요합니다.
- 프로세스 서버는 디스크 기반 캐시를 사용합니다. 네트워크 병목 또는 중단 시 데이터 변경 사항을 빠르게 저장하기 위해 권장 용량의 캐시 공간과 디스크 처리량을 사용할 수 있는지 확인하세요.
- 프로세스 서버가 데이터를 마스터 대상 서버로 업로드하여 지속적 데이터 보호를 제공하려면 충분한 대역폭이 필요합니다.

표 2에 프로세스 서버 지침이 요약되어 있습니다.

**데이터 변경률** | **CPU** | **메모리** | **캐시 디스크 크기**| **캐시 디스크 처리량** | **대역폭 수신/송신**
--- | --- | --- | --- | --- | ---
< 300GB | 4개 vCPU(2개 소켓 * 2코어 @ 2.5GHz) | 4GB | 600GB | 초당 7~10MB | 30Mbps/21Mbps
300~600GB | 8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz) | 6GB | 600GB | 초당 11~15MB | 60Mbps/42Mbps
600GB~ 1TB | 12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz) | 8GB | 600GB | 초당 16~20MB | 100Mbps/70Mbps
> 1TB | 다른 프로세스 서버 배포 | | | | 

**표 2**

여기서,

- 수신은 다운로드 대역폭(원본과 프로세스 서버 간 인트라넷)입니다.
- 송신은 업로드 대역폭(프로세스 서버와 마스터 대상 서버 간 인터넷)입니다. 송신 수치는 평균 30%의 프로세스 서버 압축을 가정합니다.
- 캐시 디스크인 경우 모든 프로세스 서버에 대해 128GB 이상의 개별 OS 디스크를 사용하는 것이 좋습니다.
- 캐시 디스크 처리량을 위해서는 벤치마킹에 RAID 10 구성을 이용한 10K RPM의 8개 SAS 드라이브로 저장소를 사용했습니다.

#### 구성 서버 고려 사항

각 구성 서버는 3~4개 볼륨을 이용하여 최대 100개의 원본 컴퓨터를 지원할 수 있습니다. 배포의 규모가 더 큰 경우에는 또 다른 구성 서버를 배포하는 것이 좋습니다. 구성 서버의 기본 가상 컴퓨터 속성은 표 1을 참조하세요.

#### 마스터 대상 서버 및 저장소 계정 고려 사항

각 마스터 대상 서버의 저장소는 OS 디스크, 보존 볼륨, 데이터 디스크를 포함합니다. 보존 드라이브는 Site Recovery 포털에 정의된 보존 기간에 대해 디스크 변경 사항의 저널을 유지합니다. 마스터 대상 서버의 가상 컴퓨터 속성에 대해서는 표 1을 참조하세요. 표 3에 A4 디스크가 사용된 방식이 나와 있습니다.

**인스턴스** | **OS 디스크** | **보존** | **데이터 디스크**
--- | --- | --- | ---
 | | **보존** | **데이터 디스크**
표준 A4 | 1개 디스크(1 * 1023GB) | 1개 디스크(1 * 1023GB) | 15개 디스크(15 * 1023GB)
표준 D14 | 1개 디스크(1 * 1023GB) | 1개 디스크(1 * 1023GB) | 31개 디스크(15 * 1023GB)
표준 DS4 | 1개 디스크(1 * 1023GB) | 1개 디스크(1 * 1023GB) | 15개 디스크(15 * 1023GB)

**표 3**

마스터 대상 서버의 용량 계획은 다음에 따라 달라집니다.

- Azure 저장소의 성능 및 제한 사항
	- 표준 계층 VM에 대해 자주 활용되는 디스크의 최대 수는 단일 저장소 계정에서 약 40(디스크당 20,000/500 IOPS)입니다. [표준 저장소 계정의 확장성 목표](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) 및 [프리미엄 저장소 계정](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts)을 참조하세요.
-	일일 데이터 변경률
-	보존 볼륨 저장소.

다음 사항에 유의하세요.

- 하나의 원본을 여러 저장소 계정에 사용할 수 없습니다. 이 제한은 보호를 구성할 때 선택한 저장소 계정으로 이동하는 데이터 디스크에 적용됩니다. OS 및 보존 디스크는 일반적으로 자동으로 배포되는 저장소 계정으로 이동합니다.
- 필요한 보존 저장소 볼륨은 일일 변경률 및 보존 일수에 따라 다릅니다. 마스터 대상 서버당 필요한 보존 저장소 = 1일 원본의 총 변동 수 * 보존 일수
- 각각의 마스터 대상 서버에는 하나의 보존 볼륨만 있습니다. 보존 볼륨은 마스터 대상 서버에 연결된 디스크 사이에서 공유됩니다. 예:
	- 디스크가 5개이고 각 디스크가 원본에서 120 IOPS(8K 크기)를 생성할 경우 디스크당 240 IOPS가 됩니다(원본 IO당 대상 디스크에서 2개 작업). 240 IOPS는 Azure 내에서 디스크당 IOPS 한도인 500을 초과하지 않습니다.
	- 보존 볼륨에서는 120* 5 = 600 IOPS가 되므로 병목이 될 수 있습니다. 이 시나리오에서 적합한 전략은 보존 볼륨에 디스크를 추가하고 RAID 스트라이프 구성으로 확장하는 것입니다. 그러면 IOPS가 여러 드라이브로 분산되므로 성능이 개선됩니다. 보존 볼륨에 추가할 드라이브 수는 다음과 같습니다.
		- 원본 환경의 총 IOPS / 500
		- 원본 환경에서 일일 총 변동 수(비압축)/287GB 287GB는 일일 대상 디스크에서 지원하는 최대 처리량입니다. 이 경우 쓰기 크기로 8K를 가정하므로 이 메트릭은 쓰기 크기인 8K를 기준으로 달라집니다. 예를 들어 쓰기 크기가 4K인 경우 처리량은 287/2가 됩니다. 쓰기 크기가 16K인 경우에는 처리량이 287*2가 됩니다.
- 필요한 저장소 계정 수 = 총 원본 IOPS/10,000


## 시작하기 전에

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | --- 
**Azure 계정** | [Microsoft Azure](https://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.
**Azure 저장소** | 복제된 데이터를 저장하려면 Azure Storage 계정이 필요합니다.<br/><br/> 계정은 [표준 지역 중복 저장소 계정](../storage/storage-redundancy.md#geo-redundant-storage) 또는 [프리미엄 저장소 계정](../storage/storage-premium-storage.md)이어야 합니다.<br/><br/> 계정은 Azure Site Recovery 서비스와 같은 하위 지역에 있고 같은 구독과 연결되어야 합니다. [새 Azure 포털](../storage/storage-create-storage-account.md)을 사용하여 만든 저장소 계정을 여러 리소스 그룹으로 이동할 수는 없습니다.<br/><br/> 자세한 내용은 [Microsoft Azure Storage 소개](../storage/storage-introduction.md)를 참조하세요.
**Azure 가상 네트워크** | 구성 서버와 마스터 대상 서버를 배포할 Azure 가상 네트워크가 필요합니다. 이 네트워크는 Azure Site Recovery 자격 증명 모음과 동일한 구독 및 지역에 있어야 합니다. Express 경로 또는 VPN 연결을 통해 데이터를 복제하려는 경우 Azure 가상 네트워크가 Express 경로 연결 또는 사이트 간 VPN을 통해 온-프레미스 네트워크에 연결되어야 합니다.
**Azure 리소스** | 모든 구성 요소를 배포하기에 충분한 Azure 리소스가 있는지 확인합니다. [Azure 구독 제한](../azure-subscription-service-limits.md)을 참조하세요.
**Azure 가상 컴퓨터** | 보호할 가상 컴퓨터는 [Azure 필수 구성 요소](site-recovery-best-practices.md)를 충족해야 합니다.<br/><br/> **디스크 수** - 보호된 서버 하나에 최대 31개 디스크가 지원됩니다.<br/><br/>**디스크 크기** - 개별 디스크 용량은 1,023GB보다 클 수 없습니다.<br/><br/> **클러스터링** - 클러스터형 서버는 지원되지 않습니다.<br/><br/> **부팅** - UEFI(Unified Extensible Firmware Interface)/EFI(Extensible Firmware Interface) 부팅은 지원되지 않습니다.<br/><br/> **볼륨** - Bitlocker 암호화된 볼륨은 지원되지 않습니다.<br/><br/> **서버 이름** - 이름은 1~63자 사이여야 하며 문자, 숫자, 하이픈을 사용할 수 있습니다. 이름은 문자나 숫자로 시작하고 문자나 숫자로 끝나야 합니다. 컴퓨터가 보호된 후 Azure 이름을 수정할 수 있습니다.
**구성 서버** | Azure Site Recovery Windows Server 2012 R2 갤러리 이미지를 기반으로 하는 표준 A3 가상 컴퓨터가 구성 서버에 대한 구독에 만들어집니다. 새 클라우드 서비스에 첫 번째 인스턴스로 만들어집니다. 구성 서버에 대해 연결 유형으로 공용 인터넷을 선택하는 경우 클라우드 서비스는 예약된 공용 IP 주소를 사용하여 만들어집니다.<br/><br/> 설치 경로는 영어 문자로만 이루어져야 합니다.
**마스터 대상 서버** | Azure 가상 컴퓨터, 표준 A4, D14 또는 DS4.<br/><br/> 설치 경로는 영어 문자로만 이루어져야 합니다. 예를 들어 Linux를 실행하는 마스터 대상 서버의 경로는 **/usr/local/ASR**이어야 합니다.
**프로세스 서버** | 최신 업데이트를 설치한 Windows Server 2012 R2를 실행하는 물리적 컴퓨터나 가상 컴퓨터에 프로세스 서버를 배포할 수 있습니다. C:/에 설치합니다.<br/><br/> 보호할 컴퓨터와 동일한 네트워크 및 서브넷에 서버를 배치하는 것이 좋습니다.<br/><br/> 프로세스 서버에 VMware vSphere CLI 5.5.0을 설치합니다. ESXi 호스트에서 실행되는 가상 컴퓨터 또는 vCenter 서버로 관리하는 가상 컴퓨터를 검색하려면 프로세스 서버에 VMware vSphere CLI 구성 요소가 필요합니다.<br/><br/> 설치 경로는 영어 문자로만 이루어져야 합니다.<br/><br/> ReFS 파일 시스템은 지원되지 않습니다.
**VMware** | VMware vSphere 하이퍼바이저를 관리하는 VMWare vCenter 서버. 최신 업데이트를 설치한 vCenter 버전 5.1 또는 5.5를 실행해야 합니다.<br/><br/> 보호할 VMware 가상 컴퓨터가 포함된 하나 이상의 vSphere 하이퍼바이저. 하이퍼바이저는 최신 업데이트를 설치한 ESX/ESXi 5.1 또는 5.5를 실행해야 합니다.<br/><br/> VMware 가상 컴퓨터에 VMware 도구가 설치되어 있고 실행 중이어야 합니다. 
**Windows 컴퓨터** | Windows를 실행하는 보호된 실제 서버 또는 VMware 가상 컴퓨터에는 몇 가지 요구 사항이 있습니다.<br/><br/> 지원되는 64비트 운영 체제: **Windows Server 2012 R2**, **Windows Server 2012** 또는 **Windows Server 2008 R2 SP1 이상**.<br/><br/> 호스트 이름, 탑재 지점, 장치 이름, Windows 시스템 경로(예: C:\\Windows)에는 영어만 사용해야 합니다.<br/><br/> 운영 체제는 C:\\ 드라이브에 설치해야 합니다.<br/><br/> 기본 디스크만 지원됩니다. 동적 디스크는 지원되지 않습니다.<br/><br/> 보호된 컴퓨터의 방화벽 규칙에서 Azure의 구성 서버와 마스터 대상 서버에 연결할 수 있도록 허용해야 합니다.p><p>Windows 서버에 모바일 서비스를 강제 설치하려면 관리자 계정(Windows 컴퓨터의 로컬 관리자여야 함)을 제공해야 합니다. 관리자 계정이 도메인 계정이 아닌 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. 그러려면 HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System에서 값이 1인 LocalAccountTokenFilterPolicy DWORD 레지스트리 항목을 추가합니다. CLI에서 레지스트리 항목을 추가하려면 cmd 또는 powershell을 열고 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**을 입력합니다. 액세스 제어에 대해 [자세히 알아보세요](https://msdn.microsoft.com/library/aa826699.aspx).<br/><br/> 장애 조치(Failover) 후에 원격 데스크톱을 사용하여 Azure의 Windows 가상 컴퓨터에 연결하려는 경우 온-프레미스 컴퓨터에 대해 원격 데스크톱이 사용하도록 설정되었는지 확인합니다. VPN을 통해 연결하지 않는 경우 방화벽 규칙에서 인터넷을 통한 원격 데스크톱 연결을 허용해야 합니다.
**Linux 컴퓨터** | 지원되는 64비트 운영 체제: **Centos 6.4, 6.5, 6.6**, **Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> 보호된 컴퓨터의 방화벽 규칙에서 Azure의 구성 서버와 마스터 대상 서버에 연결할 수 있도록 허용해야 합니다.<br/><br/> 보호된 컴퓨터의 /etc/hosts 파일은 로컬 호스트 이름을 모든 NIC와 연관된 IP 주소에 매핑하는 항목을 포함해야 합니다. <br/><br/> 장애 조치(failover) 후에 보안 셸 클라이언트(ssh)를 사용하여 Linux를 실행하는 Azure 가상 컴퓨터에 연결하려는 경우 보호된 컴퓨터의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되었는지, 그리고 방화벽 규칙에서 ssh 연결을 허용하는지 확인합니다.<br/><br/> 호스트 이름, 탑재 지점, 장치 이름 및 Linux 시스템 경로와 파일 이름(예: /etc/, /usr)에는 영어만 사용해야 합니다.<br/><br/> 다음 저장소가 있는 온-프레미스 컴퓨터에 대해 보호를 사용하도록 설정할 수 있습니다.-<br>파일 시스템: EXT3, ETX4, ReiserFS, XFS<br>다중 경로 소프트웨어 장치 매퍼(multipath)<br>볼륨 관리자: LVM2<br>HP CCISS 컨트롤러 저장소가 있는 실제 서버는 지원되지 않습니다.
**타사** | 이 시나리오의 일부 배포 구성 요소는 제대로 작동하기 위해 타사 소프트웨어를 사용합니다. 전체 목록은 [타사 소프트웨어 통지 및 정보](#third-party)를 참조하세요.


### 네트워크 연결

온-프레미스 사이트와 인프라 구성 요소(구성 서버, 마스터 대상 서버)가 배포된 Azure 가상 네트워크 간에 네트워크 연결을 구성하는 두 가지 옵션이 있습니다. 구성 서버를 배포하기 전에 사용할 네트워크 연결 옵션을 결정해야 합니다. 배포 시 이 설정을 선택해야 합니다. 나중에 변경할 수 없습니다.

**인터넷:** 온-프레미스 서버(프로세스 서버, 보호된 컴퓨터)와 Azure 인프라 구성 요소 서버(구성 서버, 마스터 대상 서버) 간의 통신 및 데이터 복제는 온-프레미스에서 구성 서버 및 마스터 대상 서버에 있는 공용 끝점 방향으로의 보안 SSL/TLS 연결을 통해 수행됩니다. (유일한 예외는 프로세스 서버와 암호화되지 않은 TCP 포트 9080의 마스터 대상 서버 간의 연결입니다. 복제 설치를 위한 복제 프로토콜에 관련된 제어 정보만 이 연결에서 교환됩니다.)

![배포 다이어그램 인터넷](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**: 온-프레미스 서버(프로세스 서버, 보호된 컴퓨터)와 Azure 인프라 구성 요소 서버(구성 서버, 마스터 대상 서버) 간의 통신 및 데이터 복제는 온-프레미스 네트워크와 구성 서버 및 마스터 대상 서버가 배포된 Azure 가상 네트워크 간의 VPN 연결을 통해 수행됩니다. 온-프레미스 네트워크가 Express 경로 연결 또는 사이트 간 VPN 연결을 통해 Azure 가상 네트워크에 연결되어 있는지 확인합니다.

![배포 다이어그램 VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)


## 1단계: 자격 증명 모음 만들기

1. [관리 포털](https://portal.azure.com)에 로그인합니다.


2. **데이터 서비스** > **복구 서비스**를 확장하고 **사이트 복구 자격 증명 모음**을 클릭합니다.


3. **새로 만들기** > **빠른 생성**을 클릭합니다.

4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. **지역**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 [Azure 사이트 복구 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하십시오.

6. **자격 증명 모음 만들기**를 클릭합니다.

	![새 자격 증명 모음](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 **복구 서비스** 페이지에서 **활성**으로 나열됩니다.

## 2단계: 구성 서버 배포

### 서버 설정 구성

1. **복구 서비스** 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![빠른 시작 아이콘](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)

2. 드롭다운 목록에서 **VMWare/물리적 서버가 포함된 온-프레미스 사이트와 Azure 간**을 선택합니다.
3. **대상(Azure) 리소스 준비**에서 **구성 서버 배포**를 클릭합니다.

	![구성 서버 배포](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)

4. **새 구성 서버 세부 정보**에 다음을 지정합니다.

	- 구성 서버 이름 및 연결에 필요한 자격 증명
	- 네트워크 연결 유형 드롭다운에서 **공용 인터넷** 또는 **VPN**을 선택합니다. 이 설정이 적용된 후에는 수정할 수 없습니다.
	- 서버를 배치할 Azure 네트워크를 선택합니다. VPN을 사용하는 경우에는 Azure 네트워크가 예상대로 온-프레미스 네트워크에 연결되어 있는지 확인합니다.
	- 서버에 할당할 내부 IP 주소 및 서브넷을 지정합니다. 내부 Azure 사용을 위해 모든 서브넷의 맨 앞 4개 IP 주소가 예약됩니다. 다른 IP 주소를 사용하세요.
	
	![구성 서버 배포](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)

5. **확인**을 클릭하면 Azure Site Recovery Windows Server 2012 R2 갤러리 이미지를 기반으로 하는 표준 A3 가상 컴퓨터가 구성 서버 구독에 만들어집니다. 새 클라우드 서비스에 첫 번째 인스턴스로 만들어집니다. 인터넷을 통해 연결하기로 선택하면, 클라우드 서비스는 예약된 공용 IP주소를 사용하여 만들어집니다. **작업** 탭에서 진행률을 모니터링할 수 있습니다.

	![진행률 모니터링](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)

6.  인터넷을 통해 연결하는 경우, 구성 서버가 배포된 후 Azure 포털의 **가상 컴퓨터** 페이지에서 해당 서버에 할당된 공용 IP 주소를 적어둡니다. 그런 다음 **끝점** 탭에서 개인 포트 443에 매핑된 공용 HTTPS 포트를 확인하고 적어둡니다. 이 정보는 나중에 마스터 대상 서버와 프로세스 서버를 구성 서버에 등록할 때 필요합니다. 이러한 끝점을 사용하여 구성 서버가 배포됩니다.

	- HTTPS: 인터넷으로 구성 요소 서버와 Azure 간의 통신을 조정하는 데 사용되는 공용 포트입니다. 개인 포트 443은 VPN으로 구성 요소 서버와 Azure 간의 통신을 조정하는 데 사용됩니다.
	- 사용자 지정: 공용 포트가 인터넷을 통한 장애 복구 도구 통신에 사용됩니다. 개인 포트 9443은 VPN에서 장애 복구 도구 통신에 사용됩니다.
	- PowerShell: 개인 포트 5986
	- 원격 데스크톱: 개인 포트 3389
	
	![VM 끝점](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

    >[AZURE.WARNING] 구성 서버 배포 중 생성된 끝점의 공용 또는 개인 포트 번호를 삭제 또는 변경하지 마세요.

구성 서버가 자동으로 생성된 Azure 클라우드 서비스에 예약된 IP 주소로 배포됩니다. 클라우드 서비스의 가상 컴퓨터(구성 서버 포함)를 재부팅해도 구성 서버 클라우드 서비스 IP 주소가 동일하게 유지되도록 하려면 예약 주소가 필요합니다. 구성 서버를 해제할 경우 예약된 공용 IP 주소를 수동으로 예약 취소해야 합니다. 그렇지 않을 경우 예약된 상태로 유지됩니다. 기본적으로 구독당 예약된 공용 IP 주소 수는 20개로 제한됩니다. 예약된 IP 주소에 대해 [자세히 알아보세요](../virtual-network/virtual-networks-reserved-private-ip.md).

### 자격 증명 모음에 구성 서버 등록

1. **빠른 시작** 페이지에서 **대상 리소스 준비** > **등록 키 다운로드**를 클릭합니다. 키 파일이 자동으로 생성됩니다. 이 파일은 생성된 날로부터 5일간 유효합니다. 구성 서버로 복사합니다.
2. **가상 컴퓨터**에서 가상 컴퓨터 목록의 구성 서버를 선택합니다. **대시보드** 탭을 열고 **연결**을 클릭합니다. 다운로드한 RDP 파일을 **열고** 원격 데스크톱을 사용하여 구성 서버에 로그온합니다. VPN을 사용하는 경우에는, 온-프레미스 사이트의 원격 데스크톱 연결에 내부 IP 주소(구성 서버를 배포할 때 지정한 주소)를 사용합니다. 처음 로그온하는 경우 Azure Site Recovery 구성 서버 설치 마법사가 자동으로 실행됩니다.

	![등록](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)

3. **타사 소프트웨어 설치**에서 **동의함**을 클릭하고 MySQL을 다운로드 및 설치합니다.

	![MySQL 설치](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)

4. **MySQL 서버 세부 정보**에서 MySQL 서버 인스턴스로 로그인하는 데 사용할 자격 증명을 만듭니다.

	![MySQL 자격 증명](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)

5. **인터넷 설정**에서 구성 서버가 인터넷에 연결하는 방식을 지정합니다. 다음 사항에 유의하세요.

	- 사용자 지정 프록시를 사용하려는 경우 공급자를 설치하기 전에 설정해야 합니다.
	- **다음**을 클릭하면 프록시 연결을 확인하는 테스트가 실행됩니다.
	- 사용자 지정 프록시를 사용하거나 기본 프록시에 인증이 필요한 경우 주소, 포트, 자격 증명을 포함한 프록시 정보를 입력해야 합니다.
	- 다음 URL은 프록시를 통해 액세스할 수 있습니다.
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- IP 주소 기반 방화벽 규칙이 구성 서버에서 [Azure 데이터 센터 IP 주소](https://msdn.microsoft.com/library/azure/dn175718.aspx)에 설명된 IP 주소 및 HTTPS(443) 프로토콜로 연결하는 통신을 허용하는지 확인하세요. 사용하려는 Azure 지역 및 미국 서부의 IP 범위를 허용해야 합니다.

	![프록시 등록](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)

6. **공급자 오류 메시지 현지화 설정**에서 오류 메시지를 표시할 언어를 지정합니다.

	![오류 메시지 등록](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)

7. **Azure Site Recovery 등록**에서 서버로 복사한 키 파일을 찾고 선택합니다.

	![키 파일 등록](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)

8. 마법사 완료 페이지에서 다음 옵션을 선택합니다.

	- **계정 관리 시작 대화 상자**를 선택하고 마법사를 마친 다음 계정 관리 대화 상자를 열도록 지정합니다.
	- **Cspsconfigtool의 바탕 화면 아이콘 만들기**를 선택하고 구성 서버에 대한 바탕 화면 바로 가기를 추가합니다. 그러면 **계정 관리** 대화 상자를 열 때마다 마법사로 돌아가지 않아도 됩니다.

	![등록 완료](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)

9. **마침**을 클릭하고 마법사를 완료합니다. 암호가 생성됩니다. 암호를 안전한 위치에 복사합니다. 프로세스 서버와 마스터 대상 서버를 구성 서버에 인증 및 등록하려면 암호가 필요합니다. 암호는 구성 서버 통신에서 채널 무결성을 보장하는 데에도 사용됩니다. 암호를 다시 생성할 수 있지만 새 암호를 사용하여 마스터 대상 서버와 프로세스 서버를 다시 등록해야 합니다.

	![암호](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

등록하면 자격 증명 모음의 **구성 서버** 페이지에 구성 서버가 나열됩니다.

### 계정 설정 및 관리

배포 중 Site Recovery는 다음 작업에 대한 자격 증명을 요청합니다.

- 사이트 복구가 vCenter 서버나 vSphere 호스트에서 VM을 자동으로 검색할 수 있도록 하는 VMware 계정.
- Site Recovery에서 모바일 서비스를 설치할 수 있도록 보호용 컴퓨터를 추가할 경우

구성 서버를 등록한 다음 **계정 관리** 대화 상자를 열고 이러한 작업에 사용해야 하는 계정을 추가 및 관리할 수 있습니다. 몇 가지 방법이 있습니다.

- 구성 서버의 마지막 설정 페이지에서 대화 상자에 대해 만든 바로 가기를 엽니다(cspsconfigtool).
- 구성 서버 설정이 종료되면 대화 상자를 엽니다.

1. **계정 관리**에서 **계정 추가**를 클릭합니다. 기존 계정을 수정 및 삭제할 수도 있습니다.

	![계정 관리](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)

2. **계정 세부 정보**에서 Azure에서 사용할 계정 이름과 자격 증명(도메인/사용자 이름)을 지정합니다.

	![계정 관리](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### 구성 서버에 연결 

구성 서버에 연결하는 방법은 두 가지입니다.

- VPN 사이트 간 연결 또는 Express 경로를 통해
- 인터넷을 통해

다음 사항에 유의하세요.

- 인터넷 연결은 서버의 공용 가상 IP 주소와 함께 가상 컴퓨터의 끝점을 사용합니다.
- VPN 연결은 끝점 개인 포트와 함께 서버의 내부 IP 주소를 사용합니다.
- 온-프레미스 서버에서 VPN 연결 또는 인터넷을 통해 Azure를 실행하는 다양한 구성 요소 서버(구성 서버, 마스터 대상 서버)에 연결(데이터 제어 및 복제)할지 여부를 한 번만 결정합니다. 나중에 이 설정을 변경할 수 없습니다. 변경할 경우 시나리오를 다시 배포하고 시스템을 다시 보호해야 합니다.


## 3단계: 마스터 대상 서버 배포

1. **대상(Azure) 리소스 준비** > **마스터 대상 서버 배포**를 클릭합니다.
2. 마스터 대상 서버 정보와 자격 증명을 지정합니다. 이 서버는 구성 서버와 동일한 Azure 네트워크에 배포됩니다. 클릭하여 완료하면 Windows 또는 Linux 갤러리 이미지를 사용하여 Azure 가상 컴퓨터가 만들어집니다.

	![대상 서버 설정](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

내부 Azure 사용을 위해 모든 서브넷의 맨 앞 4개 IP 주소가 예약됩니다. 사용 가능한 다른 IP 주소를 사용하세요.

>[AZURE.NOTE] [프리미엄 저장소 계정](../storage/storage-premium-storage.md)을 사용하여 I/O를 많이 사용하는 작업을 호스트하기 위해 높은 I/O 성능과 짧은 대기 시간을 계속 유지해야 하는 작업의 경우 보호를 구성할 때 표준 DS4를 선택합니다.


3. 이러한 끝점을 사용하여 Windows 마스터 대상 서버 VM이 만들어집니다. 공용 끝점은 인터넷을 통해 연결하는 경우에만 만들어집니다.

	- 사용자 지정: 프로세스 서버에서 공용 포트를 사용하여 인터넷을 통해 복제 데이터를 보냅니다. 프로세스 서버가 개인 포트 9443을 사용하여 VPN을 통해 데이터를 마스터 대상 서버로 보냅니다.
	- 사용자 지정1: 프로세스 서버에서 공용 포트를 사용하여 인터넷을 통해 메타데이터를 보냅니다. 개인 포트 9080은 프로세스 서버가 VPN을 통해 메타데이터를 마스터 대상 서버로 보내는 데 사용됩니다.
	- PowerShell: 개인 포트 5986
	- 원격 데스크톱: 개인 포트 3389

4. 이러한 끝점을 사용하여 Linux 마스터 대상 서버 VM이 만들어집니다. 공용 끝점은 인터넷을 통해 연결하는 경우에만 만들어집니다.

	- 사용자 지정: 프로세스 서버에서 공용 포트를 사용하여 인터넷을 통해 복제 데이터를 보냅니다. 프로세스 서버가 개인 포트 9443을 사용하여 VPN을 통해 데이터를 마스터 대상 서버로 보냅니다.
	- 사용자 지정1: 공용 포트는 프로세스 서버가 인터넷을 통해 메타데이터를 보내는 데 사용됩니다. 개인 포트 9080은 프로세스 서버가 VPN을 통해 메타데이터를 마스터 대상 서버로 보내는 데 사용됩니다.
	- SSH: 개인 포트 22

    >[AZURE.WARNING] 마스터 대상 서버 배포 중 생성된 끝점의 공용 또는 개인 포트 번호를 삭제 또는 변경하지 마세요.

5. **가상 컴퓨터**에서 가상 컴퓨터가 시작될 때까지 기다립니다.

	- Windows 서버인 경우에는 원격 데스크톱 세부 정보를 적어둡니다.
	- Linux 서버이고 VPN을 통해 연결하는 경우에는 가상 컴퓨터의 내부 IP 주소를 적어둡니다. 인터넷을 통해 연결하는 경우 공용 IP 주소를 적어둡니다.

6.  서버에 로그온하여 설치를 완료하고 구성 서버에 등록합니다.
7.  Windows를 실행하는 경우 다음을 수행합니다.

	1. 가상 컴퓨터에 대한 원격 데스크톱 연결을 시작합니다. 처음 로그온하는 경우 PowerShell 창에서 스크립트가 실행됩니다. 창을 닫지 마세요. 완료되면 호스트 에이전트 구성 도구가 자동으로 열려 서버를 등록합니다.
	2. **호스트 에이전트 구성**에서 구성 서버의 내부 IP 주소와 포트 443을 지정합니다. 가상 컴퓨터가 구성 서버와 동일한 Azure 네트워크에 연결되어 있으므로 VPN을 통해 연결하지 않는 경우에도 내부 주소와 개인 포트 443을 사용할 수 있습니다. **HTTPS 사용**을 설정된 상태로 둡니다. 앞에서 적어둔 구성 서버의 암호를 입력합니다. **확인**을 클릭하여 서버를 등록합니다. NAT 옵션은 무시해도 됩니다. 해당 옵션은 사용되지 않습니다.
	3. 보존 드라이브에 필요한 용량이 1TB 이상으로 예상될 경우 가상 디스크와 [저장소 공간](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)을 사용하여 보존 볼륨(R:)을 구성할 수 있습니다.
	
	![Windows 마스터 대상 서버](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)

8. Linux를 실행하는 경우 다음을 수행합니다.
	1. 마스터 대상 서버를 설치하기 전에 최신 LIS(Linux Integration Services)를 설치했는지 확인합니다. [여기](https://www.microsoft.com/download/details.aspx?id=46842)에서 설치 방법에 대한 지침과 함께 최신 버전의 LIS를 찾을 수 있습니다. LIS 설치 후 컴퓨터를 다시 시작합니다.
	2. **대상(Azure) 리소스 준비**에서 **추가 소프트웨어 다운로드 및 설치(Linux 마스터 대상 서버 전용)**를 클릭합니다. 다운로드한 tar 파일을 sftp 클라이언트를 사용하여 가상 컴퓨터로 복사합니다. 또는 배포된 Linux 마스터 대상 서버에 로그온한 다음 *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409*을 사용하여 파일을 다운로드할 수 있습니다.
	2. 보안 셸 클라이언트를 사용하여 서버에 로그온합니다. VPN을 통해 Azure 네트워크에 연결되어 있는 경우 내부 IP 주소를 사용합니다. 그렇지 않으면 외부 IP 주소와 SSH 공용 끝점을 사용합니다.
	3. **tar –xvzf Microsoft-ASR\_UA\_8.4.0.0\_RHEL6-64*** ![Linux 마스터 대상 서버](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)를 실행하여 GZip 압축된 설치 프로그램에서 파일을 추출합니다.
	4. 현재 위치가 tar 파일의 내용을 추출한 디렉터리인지 확인합니다.
	5. **echo *`<passphrase>`* >passphrase.txt** 명령을 사용하여 구성 서버 암호를 로컬 파일로 복사합니다.
	6. “**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**” 명령을 실행합니다.

	![대상 서버 등록](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)

9. 약 10-15분 동안 기다린 후 페이지에서 **서버** > **구성 서버** **서버 세부 정보** 탭에 마스터 대상 서버가 등록된 것으로 나열되는지 확인합니다. Linux를 실행하며 등록되지 않은 경우 /usr/local/ASR/Vx/bin/hostconfigcli에서 호스트 구성 도구를 다시 실행합니다. 루트 권한으로 chmod를 실행하여 액세스 권한을 설정해야 합니다.

	![대상 서버 확인](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

>[AZURE.NOTE] 등록을 마친 후 마스터 대상 서버가 포털에 나열되기까지 최대 15분이 소요될 수 있습니다. 즉시 업데이트하려면 **구성 서버** 페이지에서 **새로 고침**을 클릭합니다.

## 4단계: 온-프레미스 프로세스 서버 배포

시작하기 전에 재부팅 시에도 유지되도록 프로세스 서버에서 정적 IP 주소를 구성하는 것이 좋습니다.

1. 빠른 시작 > **프로세스 서버 온-프레미스 설치** > **프로세스 서버 다운로드 및 설치**를 클릭합니다.

	![프로세스 서버 설치](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)

2.  프로세스 서버를 설치할 서버에 다운로드한 zip 파일을 복사합니다. zip 파일에는 다음 두 개의 설치 파일이 들어 있습니다.

	- Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*
	- Microsoft-ASR\_CX\_8.4.0.0\_Windows*

3. 보관 파일의 압축을 풀고 설치 파일을 서버의 한 위치에 복사합니다.
4. **Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*** 설치 파일을 실행하고 지침을 따릅니다. 이렇게 하면 배포에 필요한 타사 구성 요소가 설치됩니다.
5. 그런 다음 **Microsoft-ASR\_CX\_8.4.0.0\_Windows***를 실행합니다.
6. **서버 모드** 페이지에서 **프로세스 서버**를 선택합니다.
7. **환경 세부 정보** 페이지에서 다음을 수행합니다.


	- VMware 가상 컴퓨터를 보호하려면 **예**를 클릭합니다.
	- 물리적 서버만 보호할 경우 프로세스 서버에 VMware vCLI를 설치하지 않아도 됩니다. **아니요**를 클릭하고 계속합니다.

8. VMware vCLI를 설치할 때 다음에 주의하세요.

	- **VMware vSphere CLI 5.5.0만 지원됩니다**. 프로세스 서버는 다른 버전 또는 업데이트된 vSphere CLI에서 작동하지 않습니다.
	- [여기](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)에서 vSphere CLI 5.5.0을 다운로드합니다.
	- 프로세스 서버를 설치하기 바로 전에 vSphere CLI를 설치한 경우 설정에서 설치 여부를 감지하지 못할 경우 최대 5분간 기다린 다음 다시 시도하세요. 그러면 vSphere CLI 감지에 필요한 모든 환경 변수가 올바르게 초기화됩니다.

9.	**프로세스 서버의 NIC 선택**에서 프로세스 서버가 사용해야 하는 네트워크 어댑터를 선택합니다.

	![어댑터 선택](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)

10.	**구성 서버 세부 정보**에서:

	- IP 주소 및 포트를 지정하려면 VPN을 통해 연결하는 경우 구성 서버의 내부 IP 주소를 지정하고 포트에 443을 지정합니다. 그렇지 않으면 공용 가상 IP 주소 및 매핑된 공용 HTTP 끝점을 지정합니다.
	- 구성 서버의 암호를 입력합니다.
	- 자동 푸시를 사용하여 서비스를 설치할 때 검증을 사용하지 않도록 설정하려면 **모바일 서비스 소프트웨어 서명 검증**을 선택 취소합니다. 서명 검증을 수행하려면 프로세스 서버에서 인터넷에 연결할 수 있어야 합니다.
	- **Next**를 클릭합니다.

	![구성 서버 등록](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)


11. **설치 드라이브 선택**에서 캐시 드라이브를 선택합니다. 프로세스 서버에 사용 가능 공간이 600GB 이상인 캐시 드라이브가 필요합니다. **설치**를 클릭합니다.

	![구성 서버 등록](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)

12. 설치를 완료하려면 서버를 다시 시작해야 할 수 있습니다. **구성 서버** > **서버 정보**에서 프로세스 서버가 성공적으로 등록되었는지 확인합니다.

>[AZURE.NOTE]등록을 마친 후 프로세스 서버가 구성 서버에 나열될 때까지 최대 15분이 소요될 수 있습니다. 즉시 업데이트하려면 구성 서버 페이지 하단에 있는 새로 고침 단추를 클릭하여 구성 서버를 새로 고칩니다.
 
![프로세스 서버 유효성 검사](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

프로세스 서버를 등록할 때 모바일 서비스에 대해 서명 검증을 사용하지 않도록 설정하지 않은 경우 나중에 다음과 같이 설정할 수 있습니다.

1. 관리자 권한으로 프로세스 서버에 로그온한 다음 편집을 위해 C:\\pushinstallsvc\\pushinstaller.conf 파일을 엽니다. **[PushInstaller.transport]** 섹션에서 **SignatureVerificationChecks=”0”** 줄을 추가합니다. 파일을 저장하고 닫습니다.
2. InMage PushInstall 서비스를 다시 시작합니다.


## 5단계: 사이트 복구 구성 요소 업데이트

사이트 복구 구성 요소는 수시로 업데이트됩니다. 새 업데이트를 사용할 수 있으면 다음과 같은 순서로 설치해야 합니다.

1. 구성 서버
2. 프로세스 서버
3. 마스터 대상 서버
4. 장애 복구(failback) 도구(vContinuum)

### 업데이트 확보 및 설치


1. 구성, 프로세스 및 마스터 대상 서버에 대한 업데이트는 Site Recovery **대시보드**에서 받을 수 있습니다. Linux 설치의 경우 Gzip 설치 프로그램에서 파일을 추출한 다음 “sudo ./install” 명령을 실행하여 업데이트를 설치합니다.
2. 장애 복구(failback) 도구(vContinuum)에 대한 최신 업데이트를 [다운로드](http://go.microsoft.com/fwlink/?LinkID=533813)하세요.
3. 이미 모바일 서비스가 설치된 가상 컴퓨터 또는 물리적 서버를 실행할 경우 서비스에 대해 다음과 같은 업데이트를 다운로드할 수 있습니다.

	- **옵션 1**: 업데이트를 다운로드합니다.
		- [Windows Server(64비트만 해당)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
		- [CentOS 6.4,6.5,6.6(64비트만 해당)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
		- [Oracle Enterprise Linux 6.4,6.5(64비트만 해당)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
		- [SUSE Linux Enterprise Server SP3(64비트만 해당)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
		- 프로세스 서버를 업데이트한 후에, 프로세스 서버의 C:\\pushinstallsvc\\repository 폴더에서 모바일 서비스의 업데이트된 버전을 사용할 수 있습니다.
	- **옵션 2**: 이전 버전의 모바일 서비스가 설치된 컴퓨터의 경우, 관리 포털에서 컴퓨터의 모바일 서비스를 자동으로 업그레이드할 수 있습니다.

		1. 프로세스 서버가 업데이트되어야 합니다.
		2. 업데이트가 정상적으로 작동할 수 있도록 보호된 컴퓨터가 모바일 서비스 자동 푸시를 위한 [필수 구성 요소](#install-the-mobility-service-automatically)를 충족하는지 확인합니다.
		2. 보호 그룹을 선택하고, 보호된 컴퓨터를 강조 표시한 다음 **이동성 서비스 업데이트** 클릭합니다. 이 단추를 새로운 버전의 모바일 서비스가 있는 경우에만 사용할 수 있습니다.

			![vCenter 서버 선택](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

계정 선택에서 보호된 서버에 모바일 서비스를 업데이트 하는데 사용할 관리자 계정을 지정합니다. 확인을 클릭하고 트리거된 작업이 완료되기를 기다립니다.


## 6단계: vCenter 서버 또는 vSphere 호스트 추가

1. **서버** > **구성 서버** > 구성 서버 > **vCenter Server 추가**를 클릭하여 vCenter 서버 또는 vSphere 호스트를 추가합니다.

	![vCenter 서버 선택](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)

2. 서버 또는 호스트의 세부 정보를 지정하고 서버 검색에 사용할 프로세스 서버를 선택합니다.

	- vCenter 서버가 기본 443 포트에서 실행되고 있지 않은 경우 vCenter 서버가 실행 중인 포트 번호를 지정합니다.
	- 프로세스 서버는 vCenter 서버/vSphere 호스트와 동일한 네트워크에 있어야 하며 VMware vSphere CLI 5.5.0이 설치되어 있어야 합니다.

	![vCenter 서버 설정](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)


3. 검색을 마치면 vCenter 서버가 구성 서버 세부 정보 아래에 나열됩니다.

	![vCenter 서버 설정](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)

4. 비관리자 계정을 사용하여 서버 또는 호스트를 추가하는 경우 계정에 다음과 같은 권한이 있는지 확인하세요.

	- vCenter 계정은 데이터 센터, 데이터 저장소, 폴더, 호스트, 네트워크, 리소스, 저장소 보기, 가상 컴퓨터가 있고 vSphere Distributed Switch 권한을 사용하도록 설정해야 합니다.
	- vSphere 호스트 계정은 데이터 센터, 데이터 저장소, 폴더, 호스트, 네트워크, 리소스, 가상 컴퓨터가 있고 vSphere Distributed Switch 권한을 사용하도록 설정해야 합니다.



## 7단계: 보호 그룹 만들기

1. **보호된 항목** > **보호 그룹** > **보호 그룹 만들기**를 엽니다.

	![보호 그룹 만들기](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)

2. **보호 그룹 설정 지정** 페이지에서 그룹의 이름을 지정하고 그룹을 만들 구성 서버를 선택합니다.

	![보호 그룹 설정](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)

3. **복제 설정 지정** 페이지에서 그룹의 모든 컴퓨터에 사용할 복제 설정을 구성합니다.

	![보호 그룹 복제](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)

4. 설정:
	- **다중 VM 일관성**: 이 설정을 켜면 보호 그룹의 컴퓨터에 공유된 응용 프로그램 일치 복구 지점이 만들어집니다. 이 설정은 보호 그룹의 모든 컴퓨터에서 동일한 워크로드를 실행하는 경우에 가장 적합합니다. 모든 컴퓨터가 동일한 데이터 지점으로 복구됩니다. Windows 서버에만 사용할 수 있습니다.
	- **RPO 임계값**: 연속 데이터 보호 복제 RPO가 구성된 RPO 임계값을 초과하면 경고가 생성됩니다.
	- **복구 지점 보존**: 보존 기간을 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다.
	- **응용 프로그램 일치 스냅숏 빈도**: 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 방법을 지정합니다.

**보호된 항목** 페이지에서 생성 시 보호 그룹을 모니터링할 수 있습니다.

## 8단계: 보호하려는 컴퓨터 설정

보호하려는 가상 컴퓨터 및 물리적 서버에 모바일 서비스를 설치해야 합니다. 다음 두 가지 방법으로 수행할 수 있습니다.

- 프로세스 서버에서 각 컴퓨터에 서비스를 자동으로 푸시 및 설치합니다.
- 서비스를 수동으로 설치합니다.

### 모바일 서비스 자동 설치

보호 그룹에 컴퓨터를 추가하면 프로세스 서버가 자동으로 각 컴퓨터에 모바일 서비스를 푸시 및 설치합니다.

**Windows 서버에 모바일 서비스 자동 푸시 설치:**

1. [5단계: 최신 업데이트 설치](#step-5-install-latest-updates)에 설명된 대로 프로세스 서버의 최신 업데이트를 설치하고 프로세스 서버를 사용할 수 있는지 확인합니다.
2. 원본 컴퓨터와 프로세스 서버 간 네트워크가 연결되어 있고 프로세스 서버에서 원본 컴퓨터에 액세스할 수 있는지 확인합니다.
3. **파일 및 프린터 공유**와 **Windows Management Instrumentation**을 허용하도록 Windows 방화벽을 구성합니다. Windows 방화벽 설정에서 "방화벽을 통해 앱 또는 기능 허용" 옵션을 선택하고 아래 그림과 같이 애플리케이션을 선택합니다. 도메인에 속하는 컴퓨터의 경우 GPO를 사용하여 방화벽 정책을 구성할 수 있습니다.

	![방화벽 설정](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)

4. 푸시 설치에 사용되는 계정은 보호할 컴퓨터의 Administrators 그룹에 속해야 합니다. 이러한 자격 증명은 모바일 서비스 푸시 설치에만 사용되며 보호 그룹에 컴퓨터를 추가할 때 제공해야 합니다.
5. 제공된 계정이 도메인 계정이 아닌 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. 그러려면 HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System에서 값이 1인 LocalAccountTokenFilterPolicy DWORD 레지스트리 항목을 추가합니다. CLI에서 레지스트리 항목을 추가하려면 cmd 또는 powershell을 열고 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**을(를) 입력합니다.

**Linux 서버에 모바일 서비스 자동 푸시 설치:**

1. [5단계: 최신 업데이트 설치](#step-5-install-latest-updates)에 설명된 대로 프로세스 서버의 최신 업데이트를 설치하고 프로세스 서버를 사용할 수 있는지 확인합니다.
2. 원본 컴퓨터와 프로세스 서버 간 네트워크가 연결되어 있고 프로세스 서버에서 원본 컴퓨터에 액세스할 수 있는지 확인합니다.
3. 계정이 원본 Linux 서버의 루트 사용자인지 확인합니다.
4. 원본 Linux 서버의 /etc/hosts 파일에 로컬 호스트 이름을 모든 NIC와 연결된 IP 주소에 매핑하는 항목이 포함되어 있는지 확인합니다.
5. 보호할 컴퓨터에 최신 openssh, openssh-server, openssl 패키지를 설치합니다.
6. 포트 22에 SSH를 사용하고 실행 중인지 확인합니다.
7. 다음과 같이 sshd\_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정합니다.

	- a) 루트로 로그인합니다.
	- b) /etc/ssh/sshd\_config 파일에서 **PasswordAuthentication**으로 시작하는 줄을 찾습니다.
	- c) 줄의 주석 처리를 제거하고 값을 "no"에서 "yes"로 변경합니다.

		![Linux 이동성](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)

	- d) Subsystem으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.
	
		![Linux 푸시 이동성](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)

8. 원본 컴퓨터의 Linux 변형이 지원되는지 확인합니다.
 
### 모바일 서비스 수동 설치

모바일 서비스를 설치하는 데 사용한 소프트웨어 패키지는 C:\\pushinstallsvc\\repository의 프로세스 서버에 있습니다. 프로세스 서버에 로그온하고 아래 표를 기준으로 적절한 설치 패키지를 원본 컴퓨터로 복사합니다.

| 원본 운영 체제 | 프로세스 서버의 모바일 서비스 패키지 |
|---------------------------------------------------	|------------------------------------------------------------------------------------------------------	|
| Windows Server(64비트만 해당) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6(64비트만 해당) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3(64비트만 해당) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5(64비트만 해당) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |


**Windows 서버에 모바일 서비스를 수동으로 설치하려면** 다음을 수행합니다.

1. 위 표에 나열된 프로세스 서버 디렉터리 경로에서 **Microsoft-ASR\_UA\_8.4.0.0_Windows\_GA_28Jul2015\_release.exe** 패키지를 원본 컴퓨터로 복사합니다.
2. 원본 컴퓨터에 실행 파일을 실행하여 모바일 서비스를 설치합니다.
3. 설치 프로그램의 지침을 따르세요.
4. 역할로 **모바일 서비스**를 선택하고 **다음**을 클릭합니다.
	
	![모바일 서비스 설치](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)

5. 기본 설치 경로를 설치 디렉터리로 그대로 두고 **설치**를 클릭합니다.
6. **호스트 에이전트 구성**에서 구성 서버의 IP 주소와 HTTPS 포트를 지정합니다.

	- 인터넷을 통해 연결하는 경우 공용 가상 IP 주소와 포트(공용 HTTPS 끝점)을 지정합니다.
	- VPN을 통해 연결하는 경우 내부 IP 주소와 포트 443을 지정합니다. **HTTPS 사용**을 선택된 상태로 둡니다.

	![모바일 서비스 설치](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)

7. 구성 서버 암호를 지정하고 **확인**을 클릭하여 구성 서버에 모바일 서비스를 등록합니다.

**명령줄에서 실행하려면:**

1. CX의 암호를 서버의 "C:\\connection.passphrase" 파일로 복사한 다음 이 명령을 실행합니다. 이 예제에서 CX는 104.40.75.37이며 HTTPS 포트는 62519입니다.

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Linux 서버에 모바일 서비스 수동 설치**:

1. 위 표를 기준으로 프로세스 서버에서 원본 컴퓨터로 적절한 tar를 복사합니다.
2. 셸 프로그램을 열고 `tar -xvzf Microsoft-ASR_UA_8.2.0.0*`을 실행하여 압축된 tar 아카이브를 로컬 경로로 추출합니다.
3. 셸에서 *`echo <passphrase> >passphrase.txt`*를 입력하여 tar 아카이브의 내용을 추출한 로컬 디렉터리에 passphrase.txt 파일을 만듭니다.
4. *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*를 입력하여 모바일 서비스를 설치합니다.
5. IP 주소와 포트를 지정합니다.

	- 인터넷을 통해 구성 서버에 연결하는 경우 `<IP address>` 및 `<port>`에서 구성 서버 가상 공용 IP 주소 및 공용 HTTPS 끝점을 지정합니다.
	- VPN을 통해 연결하는 경우 내부 IP 주소와 포트 443을 지정합니다.

**명령줄에서 실행하려면**:

1. CX의 암호를 서버의 "passphrase.txt" 파일로 복사한 다음 이 명령을 실행합니다. 이 예제에서 CX는 104.40.75.37이며 HTTPS 포트는 62519입니다.

운영 서버에 설치하려면

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
대상 서버에 설치하려면


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE] 이미 적절한 버전의 모바일 서비스를 실행 중인 보호 그룹에 컴퓨터를 추가하면 푸시 설치가 생략됩니다.


## 단계 9: 보호 사용

보호를 사용하려면 가상 컴퓨터와 물리적 서버를 보호 그룹에 추가합니다. 시작하기 전에 다음 사항에 주의하세요.

- 가상 컴퓨터는 15분 마다 검색되며 검색 후 Azure Site Recovery에 표시될 때까지 최대 15분이 소요될 수 있습니다.
- Site Recovery에서 가상 컴퓨터의 환경 변경 사항(예: VMware 도구 설치)이 업데이트되는 데 최대 15분이 소요됩니다.
- **구성 서버** 페이지에서 vCenter Server/ESXi 호스트에 대한 **마지막 연락** 필드에서 마지막으로 검색된 시간을 확인할 수 있습니다.
- 이미 보호 그룹이 생성된 다음 vCenter Server 또는 ESXi 호스트를 추가할 경우 Azure Site Recovery 포털이 새로 고쳐지고 가상 컴퓨터가 **보호 그룹에 컴퓨터 추가** 대화 상자에 나열될 때까지 15분이 소요됩니다.
- 보호 그룹에 컴퓨터를 추가한 다음 예약된 검색을 기다리지 않고 즉시 계속하려면 구성 서버를 강조 표시하고(클릭하지 마세요) **새로 고침** 단추를 클릭합니다.
- 보호 그룹에 가상 컴퓨터 또는 물리적 컴퓨터를 추가할 경우 모바일 서비스가 이미 설치되어 있지 않으면 프로세스 서버가 원본 서버에 모바일 서비스를 자동으로 푸시 및 설치합니다.
- 자동 푸시 메커니즘이 작동하려면 이전 단계에서 설명한 대로 보호된 컴퓨터를 설정했는지 확인합니다.

다음 단계에 따라 컴퓨터를 추가합니다.

1. **보호된 항목** > **보호 그룹** > **컴퓨터** > **컴퓨터 추가**를 클릭합니다. 특정 응용 프로그램을 실행하는 컴퓨터를 동일한 그룹에 추가하도록 보호 그룹이 작업을 미러링하는 것이 가장 좋습니다.
2. **가상 컴퓨터 선택**에서 물리적 서버를 보호하는 경우 **물리적 컴퓨터 추가** 마법사에 IP 주소와 이름을 입력합니다. 그런 다음 운영 체제 제품군을 선택합니다.

	![V-Center Server 추가](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)

3. **가상 컴퓨터 선택**에서 VMware 가상 컴퓨터를 보호하는 경우 가상 컴퓨터(EXSi 호스트를 실행 중인 경우 EXSi 호스트)를 관리하는 vCenter Server를 선택한 다음 컴퓨터를 선택합니다.

	![V-Center Server 추가](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png)
4. **대상 리소스 지정**에서 복제에 사용할 마스터 대상 서버 및 저장소를 선택하고 설정을 모든 워크로드에 사용해야 할지 여부를 선택합니다. IO를 많이 사용하는 작업을 호스트하기 위해 일관된 IO 고성능과 짧은 대기 시간이 요구되는 워크로드에 대한 보호를 구성하는 동안에는 [프리미엄 저장소 계정](../storage/storage-premium-storage.md)을 선택합니다. 워크로드 디스크에 프리미엄 저장소 계정을 사용하려는 경우 DS 시리즈의 마스터 대상을 사용해야 합니다. DS 시리즈가 아닌 마스터 대상의 경우 프리미엄 저장소 디스크를 사용할 수 없습니다.

	>[AZURE.NOTE] 여러 리소스 그룹에 [새 Azure 포털](../storage/storage-create-storage-account.md)을 사용하여 만든 저장소 계정의 이동을 지원하지 않습니다.

	![vCenter Server](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)

5. **계정 지정**에서 보호된 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다. 모바일 서비스의 자동 설치에 계정 자격 증명이 필요합니다. 계정을 선택할 수 없을 경우 2단계의 설명에 따라 설정합니다. 이 계정은 Azure로 액세스할 수 없습니다. Windows 서버의 경우 원본 서버에서 계정에 관리자 권한이 있어야 합니다. Linux의 경우 계정은 반드시 루트여야 합니다.

	![Linux 자격 증명](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)

6. 확인 표시를 클릭하여 보호 그룹에 컴퓨터 추가를 완료하고 각 컴퓨터에 대해 초기 복제를 시작합니다. **작업** 페이지에서 상태를 모니터링할 수 있습니다.

	![V-Center Server 추가](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)

7. 또한 **보호된 항목** > 보호 그룹 이름 > **가상 컴퓨터**를 클릭하여 보호 상태를 모니터링할 수 있습니다. 초기 복제가 완료되고 컴퓨터가 데이터를 동기화하면 **보호됨** 상태로 표시됩니다.

	![가상 컴퓨터 작업](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)


### 보호된 컴퓨터 속성 설정

1. 컴퓨터가 **보호됨** 상태이면 해당 장애 조치(failover) 속성을 구성할 수 있습니다. 보호 그룹 정보에서 컴퓨터를 선택하고 **구성** 탭을 엽니다.
2. 장애 조치(Failover) 후에 Azure에서 컴퓨터에 지정될 이름과 Azure 가상 컴퓨터 크기를 수정할 수 있습니다. 장애 조치(Failover) 후에 컴퓨터가 연결되는 Azure 네트워크를 선택할 수도 있습니다.

	> [AZURE.NOTE] [Migration of networks](../resource-group-move-resources.md) 작업은 Site Recovery를 배포하는 데 사용되는 네트워크에서 같은 구독 내에 있거나 여러 구독에 있는 리소스 그룹에 대해 수행할 수 없습니다.

	![가상 컴퓨터 등록 설정](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

다음 사항에 유의하세요.

- Azure 컴퓨터의 이름은 Azure 요구 사항을 준수해야 합니다.
- 기본적으로 Azure의 복제된 가상 컴퓨터는 Azure 네트워크에 연결되지 않습니다. 복제된 가상 컴퓨터가 통신할 수 있게 하려면 해당 컴퓨터에 대해 동일한 Azure 네트워크를 설정해야 합니다.
- VMware 가상 컴퓨터 또는 물리적 서버에서 볼륨 크기를 조정할 경우 심각한 상태가 됩니다. 크기를 수정해야 할 경우 다음을 실행합니다.

	- a) 크기 설정을 변경합니다.
	- b) **가상 컴퓨터** 탭에서 가상 컴퓨터를 클릭하고 **제거**를 선택합니다.
	- c) **가상 컴퓨터 제거**에서 **보호 사용 안 함(복구 드릴 및 볼륨 크기 조정에 사용)** 옵션을 선택합니다. 이 옵션을 선택하면 보호를 사용하지 않지만 Azure에 복구 지점을 유지합니다.

		![가상 컴퓨터 등록 설정](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)

	- d) 가상 컴퓨터에 대한 보호를 다시 사용하도록 설정합니다. 보호를 사용하도록 다시 설정할 경우 크기 조정된 볼륨의 데이터가 Azure로 전송됩니다.

	

## 10단계: 장애 조치(Failover) 실행

현재는 보호된 VMware 가상 컴퓨터 및 물리적 서버에 대해 계획되지 않은 장애 조치(Failover)만 실행할 수 있습니다. 다음 사항에 유의하세요.



- 장애 조치(Failover)를 시작하기 전에 구성 및 마스터 대상 서버가 실행 중이고 정상 상태인지 확인합니다. 그렇지 않으면 장애 조치(Failover)가 실패합니다.
- 계획되지 않은 장애 조치(Failover) 시 원본 컴퓨터는 종료되지 않습니다. 계획되지 않은 장애 조치(Failover)를 실행하면 보호된 서버에 대한 데이터 복제가 중지됩니다. 계획되지 않은 장애 조치(Failover)가 완료된 다음 컴퓨터 보호를 시작하려면 보호 그룹에서 컴퓨터를 삭제하고 다시 추가해야 합니다.
- 데이터 손실 없이 장애 조치(Failover)를 실행하려면 장애 조치(Failover)를 시작하기 전에 기본 사이트 가상 컴퓨터가 꺼져 있는지 확인합니다.

1. **복구 계획** 페이지에서 복구 계획을 추가합니다. 계획의 세부 정보를 지정하고 대상으로 **Azure**를 선택합니다.

	![복구 계획 구성](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)

2. **가상 컴퓨터 선택**에서 보호 그룹을 선택한 다음 그룹에서 복구 계획에 추가할 컴퓨터를 선택합니다. [자세한](site-recovery-create-recovery-plans.md) 복구 계획을 알아봅니다.

	![가상 컴퓨터 추가](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)

3. 필요한 경우 계획을 사용자 지정하여 그룹을 만들고 복구 계획의 컴퓨터가 장애 조치(Failover)되는 순서를 지정할 수 있습니다. 수동 작업 및 스크립트에 대한 프롬프트를 추가할 수도 있습니다. Azure로 복구할 때 스크립트는 [Azure 자동화 Runbook](site-recovery-runbook-automation.md)을 사용하여 추가할 수 있습니다.

4. **복구 계획** 페이지에서 계획을 선택하고 **계획되지 않은 장애 조치(Failover)**를 클릭합니다.
5. **장애 조치(Failover) 확인**에서 장애 조치(Failover) 방향(Azure로)을 확인하고 장애 조치(Failover)할 복구 지점을 선택합니다.
6. 장애 조치(Failover) 작업이 완료될 때까지 기다린 다음 장애 조치(Failover)가 예상대로 작동했는지, 그리고 복제된 가상 컴퓨터가 Azure에서 성공적으로 시작되는지 확인합니다.




## 11단계: Azure에서 장애 조치(Failover)된 컴퓨터 장애 복구

Azure에서 실행 중인 컴퓨터를 장애 조치(Failover)한 다음 온-프레미스 환경으로 복구하는 방법에 대해 [자세히 알아보세요](site-recovery-failback-azure-to-vmware-classic-legacy.md).


## 프로세스 서버 관리

프로세스 서버는 Azure의 마스터 대상 서버로 복제 데이터를 보내고 vCenter Server에 추가된 새 VMware 가상 컴퓨터를 검색합니다. 다음 상황에서 배포된 프로세스 서버를 변경할 수 있습니다.

- 현재 프로세스 서버가 중단될 경우
- RPO(복구 지점 목표)가 조직에서 승인할 수 없는 수준으로 상승한 경우

필요에 따라, 온-프레미스 VMware 가상 컴퓨터 및 물리적 서버 중 일부 또는 전체 복제본을 다른 프로세스 서버로 이동할 수 있습니다. 예:

- **실패**—프로세스 서버에 장애가 발생하거나 사용할 수 없을 경우 보호된 컴퓨터 복제를 다른 프로세스 서버로 이동할 수 있습니다. 원본 컴퓨터 및 복제 컴퓨터의 메타데이터가 새 프로세스 서버로 이동하고 데이터가 다시 동기화됩니다. 새 프로세스 서버가 vCenter Server에 자동으로 연결되고 자동 검색을 수행합니다. Site Recovery 대시보드에서 프로세스 서버 상태를 모니터링할 수 있습니다.
- **RPO 조정을 위한 부하 분산**—부하 분산을 향상하려면 사이트 복구 포털에서 다른 프로세스 서버를 선택하고 하나 이상의 컴퓨터 복제본을 선택한 서버로 이동하여 수동 부하 분산을 수행할 수 있습니다. 이 경우에는 선택한 원본 및 복제 컴퓨터의 메타데이터가 새 프로세스 서버로 이동했습니다. 원본 프로세스 서버는 vCenter Server에 연결된 상태를 유지합니다.

### 프로세스 서버 모니터링

프로세스 서버가 심각한 상태인 경우 Site Recovery 대시보드에 경고 상태가 표시됩니다. 상태를 클릭하여 이벤트 탭을 연 다음 작업 탭에서 특정 작업으로 드릴다운합니다.

### 복제에 사용된 프로세스 서버를 수정합니다.

1. **서버** > **구성 서버** > 구성 서버 > **서버 세부 정보**를 엽니다.
2. **프로세스 서버** > 수정하려는 서버 옆에 있는 **프로세스 서버 변경**을 클릭합니다.

	![프로세스 서버 1 변경](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)

3. **프로세스 서버 변경** > **대상 프로세스 서버**에서 사용할 새 서버를 선택한 다음 새 서버에 복제할 가상 컴퓨터를 선택합니다. 여유 공간 및 사용된 메모리에 대한 세부 정보는 서버 이름 옆의 정보 아이콘을 클릭합니다. 선택된 각 가상 컴퓨터를 새 프로세스 서버로 복제하는 데 필요한 평균 공간이 표시되므로 부하 결정을 할 때 도움이 됩니다.

	![프로세스 서버 2 변경](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)

4. 새 프로세스 서버로 복제를 시작하려면 확인 표시를 클릭합니다. 프로세스 서버에서 심각한 상태의 가상 컴퓨터를 모두 제거하면 대시보드에 더 이상 심각한 경고가 표시되지 않습니다.


## 타사 소프트웨어 통지 및 정보

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=AcomDC_0831_2016-->