<properties
	pageTitle="Site Recovery 배포 준비 | Microsoft Azure"
	description="Azure Site Recovery는 온-프레미스서버에 있는 가상 컴퓨터와 물리적 서버를 Azure 또는 보조 데이터센터로 복제, 장애 조치 및 복구하는 작업을 조정합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/07/2015"
	ms.author="raynew"/>

# Azure Site Recovery 배포 준비

## 이 문서의 내용

이 문서에서는 Azure Site Recovery 배포의 준비 방법을 설명합니다. 이 문서를 읽은 후 질문이 있다면 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시해 주세요.

## Hyper-V 가상 컴퓨터 보호

몇 가지 배포 방법으로 Hyper-v 가상 컴퓨터를 보호할 수 있습니다. Azure 저장소 또는 보조 데이터센터에 온-프레미스 Hyper-VM을 복제할 수 있습니다. 배포마다 요구 사항이 다릅니다.

**요구 사항** | **Azure에 복제(VMM 있음)** | **Hyper-V VM을 Azure에 복제(VMM 없음)** | **Hyper-V VM을 보조 사이트에 복제(VMM 있음)** | **세부 정보**
---|---|---|---|---
**VMM** | System Center 2012 R2에서 실행되는 VMM<br/><br/>하나 이상의 VMM 호스트 그룹을 포함하는 하나 이상의 VMM 클라우드 | 해당 없음 | 최신 업데이트가 설치된 System Center 2012 SP1 이상에서 실행 중인 기본 및 보조 사이트의 VMM 서버 <br/><br/> 각 VMM 서버에 클라우드가 하나 이상 있습니다. VMM 클라우드에는 Hyper-V 용량 프로필이 설정되어 있어야 합니다.<br/><br/> 원본 클라우드에는 VMM 호스트 그룹이 하나 이상 있어야 합니다. | 선택 사항입니다. Azure에 Hyper-V 가상 컴퓨터를 복제하기 위해 System Center VMM 배포할 필요는 없지만, 배포할 경우 VMM 서버가 제대로 설정되어 있는지 확인해야 합니다. 올바른 VMM 버전 실행하고 있으며 클라우드가 설정되었는지 확인하는 것이 여기에 포함됩니다.
**Hyper-V** | Windows Server 2012 R2 이상을 실행하는 온-프레미스 데이터센터에서 하나 이상의 Hyper-V 호스트 서버 | Windows Server 2012 R2 이상을 실행하고 인터넷에 연결된 원본 및 대상 사이트에서 하나 이상의 Hyper-V 서버<br/><br/> Hyper-V 서버는 VMM 클라우드의 호스트 그룹에 있어야 합니다. | 최신 업데이트가 설치되고 인터넷에 연결된 Windows Server 2012 이상을 실행하는 원본 및 대상 사이트에서 하나 이상의 Hyper-V 서버<br/><br/> Hyper-V 서버는 VMM 클라우드의 호스트 그룹에 있어야 합니다. | 
**가상 컴퓨터** | 원본 Hyper-V 호스트 서버에서 하나 이상의 VM | 원본 VMM 클라우드의 Hyper-V 호스트 서버에서 하나 이상의 VM | 원본 VMM 클라우드의 Hyper-V 호스트 서버에서 하나 이상의 VM. | Azure에 복제하는 VM은 [Azure 가상 컴퓨터 필수 조건](site-recovery-best-practices.md/#virtual-machines)에 부합해야 합니다.
**Azure 계정** | [Azure](http://azure.microsoft.com/) 계정과 사이트 복구 서비스 구독이 필요합니다. | [Azure](http://azure.microsoft.com/) 계정과 사이트 복구 서비스 구독이 필요합니다. | 해당 없음 | 계정이 없는 경우 [무료 평가판](pricing/free-trial/)으로 시작하세요. 이 서비스에 대한 [가격 책정](pricing/details/site-recovery/)을 읽어보세요.
**Azure 저장소** | 지역에서 복제를 사용할 수 있는 Azure 저장소 계정에 대한 구독이 필요합니다. | 지역에서 복제를 사용할 수 있는 Azure 저장소 계정에 대한 구독이 필요합니다. | 해당 없음 | 계정이 Azure Site Recovery 자격 증명 모음과 동일한 하위 지역에 있고 동일한 구독과 연결되어야 합니다. [저장소에 대해 자세히 알아보기](../storage/storage-introduction.md)
**저장소 매핑** | 해당 없음 | 해당 없음 | 장애 조치 후 가상 컴퓨터가 저장소에 최적으로 연결되도록 저장소 매핑을 선택적으로 설정할 수 있습니다. 2개의 온-프레미스 VMM 사이트 사이에서 복제하는 경우 기본적으로 복제본 가상 컴퓨터가 대상 Hyper-V 호스트 서버 상에서 표시된 곳에 저장됩니다. 원본 VMM 서버와 대상 VMM 서버에서 VMM 저장소 분류 사이에 매핑을 구성할 수 있습니다. | 이 기능을 사용하려면 배포를 시작하기 전에 저장소 분류를 설정해야 합니다. [자세히 알아보기](site-recovery-storage-mapping.md).
**SAN 복제** | 해당 없음 | 해당 없음 | SAN 복제를 통해 두 온-프레미스 VMM 사이트 간을 복제할 경우 기존 SAN 환경을 사용할 수 있습니다. | [지원되는 SAN 어레이](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)가 필요하며 VMM에서 SAN 저장소가 검색 및 분류되어야 합니다. <br/><br/>현재 복제하고 있지 않은 경우 LUN을 만들고 VMM 콘솔에 저장소를 할당해야 합니다. 이미 복제하고 있다면 이 단계를 건너뛰어도 됩니다.
**네트워킹** | 동일한 Azure 네트워크에서 장애 조치되는 모든 컴퓨터가 자신이 속한 복구 계획에 관계없이 서로 연결할 수 있도록 네트워크 매핑을 설정할 수 있습니다. 또한 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정된 경우 가상 컴퓨터가 다른 온-프레미스 가상 컴퓨터에 연결할 수 있습니다. 동일한 복구 계획에서 장애 조치되는 네트워크 매핑 컴퓨터를 설정하지 않는 경우 연결 가능합니다. | 해당 없음 | <br/><br/>장애 조치 후 가상 컴퓨터가 적합한 네트워크에 연결되고 복제본 가상 컴퓨터가 Hyper-V 호스트 서버에 최적으로 배치될 수 있게 네트워크 매핑을 설정합니다. 네트워크 매핑을 구성하지 않으면 장애 조치 후 복제된 컴퓨터가 어떤 VM 네트워크에도 연결되지 않습니다. | [여기서](site-recovery-network-mapping) 네트워크 매핑에 대해 자세히 알아봅니다. <br/><br/> VMM을 통해 네트워크 매핑을 설정하려면 VMM 논리적 및 VM 네트워크가 정확히 구성되었는지 확인해야 합니다. [자세히 보기](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) 및 [VM 네트워크](https://technet.microsoft.com/library/jj721575.aspx)를 참조하세요. 또한 [VMM의 네트워크 고려 사항](site-recovery-network-design.md/#vmm-design)에 대해서도 읽어 보세요.  
**공급자 및 에이전트** | 배포 중에 VMM 서버에 Azure Site Recovery 공급자를 설치합니다. VMM 클라우드에 위치한 Hyper-V 호스트 서버에서는 Azure Recovery Services 에이전트를 설치합니다. | 배포 중에 Hyper-V 호스트 서버나 클러스터에서 Azure Site Recovery 공급자와 Azure Recovery Services 에이전트를 모두 설치합니다.| 배포 중에 VMM 서버에 Azure Site Recovery 공급자를 설치합니다. VMM 클라우드에 위치한 Hyper-V 호스트 서버에서는 Azure Recovery Services 에이전트를 설치합니다. | 암호화된 HTTPS 연결을 사용하여 공급자와 에이전트가 인터넷을 통해 사이트 복구에 연결합니다. 공급자 연결을 위해 방화벽 예외를 추가하거나 특정 프록시를 만들 필요는 없지만 사용자 지정 프록시를 사용하려는 경우 배포에 앞서 다음 URL이 방화벽을 통과할 수 있게 허용해야 합니다.<br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net<br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/>*.store.core.windows.net **인터넷 연결** | VMM 서버만 인터넷 연결 필요 | Hyper-V 호스트 서버만 인터넷 연결 필요 |VMM 서버만 인터넷 연결 필요 | 가상 컴퓨터에는 아무 것도 설치할 필요가 없으며 가상 컴퓨터는 인터넷에 직접 연결하지 않습니다.



## VMware 가상 컴퓨터 또는 물리적 서버 보호

몇 가지 배포 방법으로 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 보호할 수 있습니다. Azure 또는 보조 데이터센터에 복제할 수 있습니다. 배포마다 요구 사항이 다릅니다.

**요구 사항** | **Azure에 VMware VM/물리적 서버복제** | **보조 사이트에 VMware VM/물리적 서버복제**  
---|---|--- 
**기본 사이트** | **프로세스 서버**: 전용 Windows 서버(실제 또는 가상) | **프로세스 서버**: 전용 Windows 서버(실제 또는 VMware 가상 컴퓨터)<br/><br/>  
**보조 온-프레미스 사이트** | 해당 없음 | **구성 서버**: 전용 Windows 서버 (실제 또는 가상) <br/><br/> **마스터 대상 서버**: 전용 서버(실제 또는 가상). Windows 컴퓨터를 보호하기 위해 Windows 또는 Linux 컴퓨터를 보호하기 위해 Linux를 구성합니다.
**Azure** | **구독**: 사이트 복구 서비스를 구독해야 합니다. [저장소 계정](pricing/details/site-recovery/) <br/><br/> **가격 책정** 정보: 지역에서 복제를 사용하는 저장소 계정이 필요합니다. 계정이 사이트 복구 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다. [자세히 알아보기](../storage/storage-introduction.md)<br/><br/> **구성 서버**: 구성 서버를 Azure VM으로 설정해야 합니다 <br/><br/> **마스터 대상 서버**: 마스터 대상 서버를 Azure VM으로 설정해야 합니다. <br/><br/> Windows 컴퓨터를 보호하기 위해 Windows 또는 Linux 컴퓨터를 보호하기 위해 Linux를 구성합니다.<br/><br/> **Azure 가상 네트워크**: 구성 서버와 마스터 대상 서버를 배포할 Azure 가상 네트워크가 필요합니다. 이 네트워크는 Azure Site Recovery 자격 증명 모음과 동일한 구독 및 지역에 있어야 합니다. | 해당 없음  
**가상 컴퓨터/물리적 서버** | 하나 이상의 VMware 가상 컴퓨터 또는 물리적 Windows/Linux 서버.<br/><br/>배포 중에 모바일 서비스가 각 컴퓨터에 설치됩니다.| 하나 이상의 VMware VM 또는 물리적 Windows/Linux 서버.<br/><br/> 배포 하는 동안 통합 에이전트가 각 컴퓨터에 설치됩니다.




## Azure 가상 컴퓨터 요구 사항

Azure에서 지원하는 운영 체제를 실행하는 가상 컴퓨터와 물리적 서버를 복제하기 위해 사이트 복구를 배포할 수 있습니다. 여기에는 대부분 버전의 Windows 및 Linux가 포함됩니다. 보호할 온-프레미스 가상 컴퓨터가 Azure 요구 사항을 준수하는지 확인해야 합니다.


**기능** | **지원** | **세부 정보**
---|---|---
Hyper-V 호스트 운영 체제 | Windows Server 2012 R2 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
VMware 하이퍼바이저 운영 체제 | 지원되는 운영 체제 실행 | [세부 정보](site-recovery-vmware-to-azure.md/#before-you-start) 
게스트 운영 체제 | Hyper-V와 Azure 간 복제의 경우 사이트 복구에서는 [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 모든 운영 체제를 지원합니다. <br/><br/> VMware와 물리적 서버 간 복제의 경우 Windows 및 Linux [필수 구성 요소](site-recovery-vmware-to-azure.md/#before-you-start)를 확인하세요. | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. 
게스트 운영 체제 아키텍처 | 64비트 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. 
데이터 디스크 수 | 16 개 이하(최대 값은 만들고자 하는 가상 컴퓨터 크기의 함수) 16 = XL) | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
데이터 디스크 VHD 크기 | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
고정 IP 주소 | 지원됨 | 주 가상 컴퓨터가 고정 IP 주소를 사용하는 경우 Azure에서 생성되는 가상 컴퓨터에 대해 고정 IP 주소를 지정할 수 있습니다.
iSCSI 디스크 | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
공유 VHD | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
FC 디스크 | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
하드 디스크 형식| VHD <br/><br/> VHDX | 현재 Azure에서는 VHDX가 지원되지 않지만 Azure로 장애 조치하면 사이트 복구가 자동으로 VHDX를 VHD로 변환합니다. 온-프레미스에 장애 복구 시 가상 컴퓨터에서 계속해서 VHDX 형식을 사용합니다.
가상 컴퓨터 이름| 1 자에서 63자 사이입니다. 문자, 숫자 및 하이픈으로 제한됩니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. | 사이트 복구에서 가상 컴퓨터 속성 내 값 업데이트
가상 컴퓨터 유형 | <p>1세대</p> <p>2세대 Windows</p> | 300GB보다 작은 VHDX의 디스크 형식인 데이터 볼륨 1 또는 2를 포함하는 기본 디스크의 OS 디스크 형식인 2세대 가상 컴퓨터입니다. Linux 2세대 가상 컴퓨터는 지원되지 않습니다. [자세한 정보](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## 배포 최적화

다음 팁을 사용하면 배포의 최적화 및 크기 조정에 도움이 됩니다.

- **운영 체제 볼륨 크기**: Azure에 가상 컴퓨터를 복제하는 경우 운영 체제 볼륨이 1TB 미만이어야 합니다. 이것보다 더 많은 볼륨이 있는 경우 배포를 시작하기 전에 볼륨을 다른 디스크로 수동으로 이동할 수 있습니다.
- **데이터 디스크 크기**: Azure에 복제하는 경우 최대 1TB의 각 가상 컴퓨터에서 최대 32 개의 데이터 디스크를 사용할 수 있습니다. ~32 TB 가상 컴퓨터를 효과적으로 복제 및 장애 조치할 수 있습니다.
- **복구 계획 제한**: 사이트 복구는 수천 개의 가상 컴퓨터로 확장할 수 있습니다. 복구 계획은 다 함께 장애 조치되어야 하는 응용 프로그램에 대한 모델로 설계되었으므로 복구 계획에서 컴퓨터의 수를 50으로 제한할 수 있습니다.
- **Azure 서비스 제한**: 모든 Azure 구독은 코어, 클라우드 서비스 등에서 일련의 기본 제한값이 제공됩니다. 구독에서 리소스의 가용성을 확인하기 위해 테스트 장애 조치를 실행하는 것이 좋습니다. Azure 지원을 통해 이러한 제한을 수정할 수 있습니다.
- **용량 계획**: Hyper-V VM을 복제하는 경우 [용량 플래너](http://www.microsoft.com/download/details.aspx?id=39057)를 확인하세요.
- **복제 대역폭**: 복제 대역폭이 부족한 경우 다음에 유의하십시오.
	- **Express 경로**: 사이트 복구는 Riverbed와 같은 Azure Express 경로 및 WAN 최적화 프로그램과 함께 작동합니다. [여기서](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) Express 경로에 대해 더 알아볼 수 있습니다.
	- **복제 트래픽**: 사이트 복구는 전체 VHD가 아닌 데이터 블록만 사용하여 스마트 초기 복제를 수행합니다. 복제가 진행 중일 때는 변경 내용만 복제됩니다.
	- **네트워크 트래픽**:[Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)(대상 IP 주소 및 포트 기반 정책 포함) 또한 Azure 백업 에이전트를 사용하여 Azure Site Recovery에 복제하는 경우. 해당 에이전트에 대한 제한을 구성할 수 있습니다. [자세히 알아보기](https://support.microsoft.com/kb/3056159).
- **RTO**: 사이트 복구를 통해 예상할 수 있는 복제 시간 개체(RTO)를 측정하고 싶다면 테스트 장애 조치를 실행하고 작업 완료까지 시간이 얼마나 걸리는지 분석합니다. Azure에 장애 조치하는 경우 최선의 RTO를 위해 Azure 자동화 및 복구 계획과 통합하여 모든 수동 작업을 자동화하는 것이 좋습니다.
- **RPO**: Azure에 복제하는 경우 사이트 복구는 가까운 동기 복구 지점 목표 (RPO)를 지원 합니다. 이것은 데이터 센터와 Azure 사이에서 충분한 대역폭을 가정합니다.





## 다음 단계

이러한 모범 사례를 검토한 후 사이트 복구의 배포를 시작할 수 있습니다.

- [온-프레미스 VMM 사이트와 Azure 간 보호 설정](site-recovery-vmm-to-azure.md)
- [온-프레미스 Hyper-V 사이트와 Azure 간 보호 설정](site-recovery-hyper-v-site-to-azure.md)
- [2개의 온-프레미스 VMM 사이트 간 보호 설정](site-recovery-vmm-to-vmm.md)
- [SAN으로 2개의 온-프레미스 VMM 사이트 간 보호 설정](site-recovery-vmm-san.md)
- [단일 VMM 서버로 보호 설정](site-recovery-single-vmm.md)
 

<!---HONumber=AcomDC_1210_2015-->