<properties
	pageTitle="Site Recovery 배포 준비 | Microsoft Azure"
	description="이 문서에서는 Azure Site Recovery를 사용하여 복제를 배포하기 위한 준비 과정을 설명합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/08/2016"
	ms.author="raynew"/>

# Azure Site Recovery 배포 준비

Azure Site Recovery 서비스는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터는 Azure 또는 보조 온-프레미스 데이터 센터로 복제할 수 있습니다. 빠른 개요를 알아보려면 [Azure Site Recovery란?](site-recovery-overview.md)을 확인하세요.

## 개요

Azure Site Recovery는 VMware 및 Hyper-V VM과 물리적 서버를 Azure 또는 보조 데이터 센터에 복제하는 기능을 지원합니다. 이 문서에서는 각 복제 시나리오에 대해 Azure Site Recovery 배포를 준비하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## Hyper-V 복제를 위한 배포 요구 사항

이 테이블에는 Azure 및 보조 사이트로 Hyper-V 복제(VMM을 사용하거나 사용하지 않고)를 수행하기 위한 일반적인 배포 요구 사항이 요약되어 있습니다. 이 테이블은 각 복제 시나리오의 일반적인 요구 사항을 이해하고 서로 비교하는 데 도움이 됩니다. 자세한 배포 필수 조건에 대한 링크도 있습니다.

**Azure에 복제(VMM 있음)** | **Azure에 복제(VMM 없음)** | **보조 사이트에 복제(VMM 있음)**
---|---|---
**VMM**: System Center 2012 R2에서 실행 중인 VMM 서버 하나 이상. VMM 서버에는 VMM 호스트 그룹이 하나 이상 포함된 클라우드가 하나 이상 있어야 합니다.<br/><br/> **Hyper-V**: Windows Server 2012 R2 이상을 실행하는 온-프레미스 데이터 센터에 Hyper-V 호스트 서버 하나 이상. Hyper-V 서버는 VM 클라우드의 호스트 그룹에 있어야 합니다.<br/><br/> **가상 컴퓨터**: 원본 Hyper-V 서버에 VM이 하나 이상 필요합니다. Azure에 복제하는 VM은 [Azure 가상 컴퓨터 필수 조건](#azure-virtual-machine-requirements)에 부합해야 합니다.<br/><br/> **Azure 계정**: [Azure](https://azure.microsoft.com/) 계정 및 구독이 필요합니다.<br/><br/> **Azure 저장소**: 복제된 데이터를 저장하려면 [Azure 저장소 계정](../storage/storage-redundancy.md#geo-redundant-storage)이 있어야 합니다. 복제된 데이터는 Azure 저장소에 저장되고 장애 조치(Failover) 발생 시 Azure VM이 작동합니다.<br/><br/> **Network mapping**: 동일한 Azure 네트워크에서 장애 조치되는 모든 컴퓨터가 자신이 속한 복구 계획에 관계없이 서로 연결할 수 있도록 네트워크 매핑을 설정할 수 있습니다. 대상 Azure 네트워크에 네트워크 게이트웨이가 있으면 가상 컴퓨터도 온-프레미스 가상 컴퓨터에 연결할 수 있습니다. 동일한 복구 계획에서 장애 조치되는 네트워크 매핑 컴퓨터를 설정하지 않는 경우 연결 가능합니다.<br/><br/> **공급자/에이전트**: 배포 중에 VMM 서버에 Azure Site Recovery Provider를 설치하고 Hyper-V 호스트 서버에 Azure 복구 서비스 에이전트를 설치합니다. 공급자는 Azure Site Recovery와 통신합니다. 에이전트는 원본과 대상 Hyper-V 서버 간의 데이터 복제를 처리합니다. VM에는 아무 것도 설치되지 않습니다.<br/><br/> **인터넷 연결**: VMM 서버 및 Hyper-V 호스트에서 연결합니다.<br/><br/> **Provider 연결**: Provider가 프록시를 통해 Site Recovery에 연결할 경우 프록시가 Site Recovery URL에 액세스할 수 있어야 합니다.<br/><br/> [구체적인 배포 필수 조건](site-recovery-vmm-to-azure.md#before-you-start) | **Hyper-V**: 원본 및 대상 사이트에서 Windows Server 2012 R2 이상을 실행하는 Hyper-V 서버 하나 이상.<br/><br/> **가상 컴퓨터**: 원본 Hyper-V 서버에 VM 하나 이상. Azure로 복제하는 VM은 [Azure 가상 컴퓨터 필수 조건](#azure-virtual-machine-requirements)을 준수해야 합니다.<br/><br/> **Azure 계정**: [Azure](https://azure.microsoft.com/) 계정 및 구독이 필요합니다.<br/><br/> **Azure 저장소**: 복제된 데이터를 저장하려면 [Azure 저장소 계정](../storage/storage-redundancy.md#geo-redundant-storage)이 있어야 합니다.<br/><br/> **공급자/에이전트**: 배포 중에 Hyper-V 호스트 서버 또는 클러스터에 Azure Site Recovery Provider 및 Azure 복구 서비스 에이전트를 모두 설치합니다. VM에는 아무 것도 설치되지 않습니다.<br/><br/> **인터넷 연결**: Hyper-V 호스트에서 연결합니다.<br/><br/> **Provider 연결**: Provider가 프록시를 통해 연결할 경우 프록시가 Site Recovery URL에 액세스할 수 있어야 합니다.<br/><br/> [구체적인 배포 필수 조건](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | **VMM**: 원본 VMM 서버에는 VMM 호스트 그룹이 하나 이상 포함된 클라우드가 하나 이상 있어야 합니다. 클라우드에 Hyper-V 기능 프로필이 설정되어 있어야 합니다. <br/><br/>**Hyper-V**: 원본 및 대상 사이트에서 최신 업데이트가 적용된 Windows Server 2012 이상을 실행하는 Hyper-V 서버 하나 이상. Hyper-V 서버는 VMM 클라우드의 호스트 그룹에 있어야 합니다.<br/><br/> **가상 컴퓨터**: 원본 VMM 클라우드에 VM 하나 이상. <br/><br/> **네트워킹 매핑**: 장애 조치(Failover) 후 가상 컴퓨터가 적합한 네트워크에 연결되고 복제본 가상 컴퓨터가 대상 Hyper-V 호스트 서버에 최적으로 배치될 수 있게 네트워크 매핑을 설정합니다. 네트워크 매핑을 구성하지 않으면 장애 조치 후 복제된 컴퓨터가 어떤 VM 네트워크에도 연결되지 않습니다.<br/><br/> **저장소 매핑**: 장애 조치(Failover) 후 가상 컴퓨터가 저장소에 최적으로 연결되도록 저장소 매핑을 설정할 수 있습니다(기본적으로 복제본 VM은 대상 Hyper-V 서버에 지정된 위치에 저장됨).<br/><br/> **SAN 복제** SAN 복제를 통해 두 온-프레미스 VMM 사이트 간에 복제할 경우 기존 SAN 환경을 사용할 수 있습니다. [지원되는 SAN 배열](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)을 확인하세요.<br/><br/> **공급자/에이전트**: 배포 중에 VMM 서버에 Azure Site Recovery Provider를 설치하여 Azure Site Recovery와 통신합니다. LAN/VPN을 통해 Hyper-V 원본과 대상 서버 간에 복제가 발생합니다.<br/><br/> **인터넷 연결**: VMM 서버에서만 연결합니다.<br/><br/> **Provider 연결**: Provider가 프록시를 통해 연결할 경우 Site Recovery URL에 액세스할 수 있어야 합니다.<br/><br/> [구체적인 배포 필수 조건](site-recovery-vmm-to-vmm.md#before-you-start)


## VMware VM 및 물리적 서버를 복제하기 위한 배포 요구 사항

이 테이블에는 Azure 및 보조 사이트로 VMware VM과 Windows/Linux 물리적 서버를 복제하기 위한 요구 사항이 요약되어 있습니다.

>[AZURE.NOTE] [향상된](site-recovery-vmware-to-azure-classic.md) 배포 모델 또는 기존 배포에 사용된 [기존](site-recovery-vmware-to-azure-classic-legacy.md) 모델을 사용하여 VMware VM 및 물리적 서버를 Azure에 복제할 수 있습니다. 아래 테이블에는 각 모델의 배포 요구 사항이 정리되어 있습니다.

**Azure에 복제(향상된 모델)** | **Azure에 복제(기존 모델)** | **보조 사이트에 복제**
---|---|---
**온-프레미스 관리 서버**: 온-프레미스 사이트에 관리 서버 역할을 할 전용 서버가 필요합니다. 모든 Site Recovery 구성 요소가 이 서버에 설치됩니다.<br/><br/> **추가 프로세스 서버**: 프로세스 서버는 기본적으로 관리 서버에 설치되지만 필요에 따라 추가 온-프레미스 프로세스 서버를 설치하여 배포 규모를 확장할 수 있습니다.<br/><br/> **VMware vCenter/ESXi**: VMware VM을 복제하는 경우(또는 물리적 서버를 장애 복구하려는 경우) VM이 배치된 vSphere ESX/ESXi가 필요합니다. ESXi 호스트를 관리하는 vCenter 서버를 두는 것이 좋습니다.</br><br/> **장애 복구**: 물리적 서버를 복제하는 경우에도 Azure에서 장애 복구를 수행하려면 VMware 환경이 필요합니다. 뿐만 아니라 프로세스 서버를 Azure VM으로 설정해야 하며 대용량 트래픽 볼륨을 장애 복구하려는 경우 추가 온-프레미스 마스터 대상 서버를 설정해야 합니다. [자세히 알아보기](site-recovery-failback-azure-to-vmware-classic.md)<br/><br/> **Azure 계정**: [Azure](https://azure.microsoft.com/) 계정 및 구독이 필요합니다.<br/><br/> **Azure 저장소**: 복제된 데이터를 저장하려면 [Azure 저장소 계정](../storage/storage-redundancy.md#geo-redundant-storage)이 있어야 합니다. 복제된 데이터는 Azure 저장소에 저장되고 장애 조치(Failover) 발생 시 Azure VM이 작동합니다.<br/><br/> **Azure 가상 네트워크**: 장애 조치(Failover) 발생 시 Azure VM에서 연결할 Azure 가상 네트워크가 필요합니다. 장애 조치(Failover) 후 장애 복구를 수행하려면 Azure 네트워크에서 온-프레미스 사이트로 VPN 연결(또는 Azure Express 경로) 설정이 필요합니다.<br/><br/> **보호된 컴퓨터**: VMware 가상 컴퓨터 또는 물리적 Windows/Linux 서버 하나 이상. 배포하는 동안 복제하려는 각 컴퓨터에 모바일 서비스가 설치됩니다.<br/><br/> **연결**: 관리 서버가 프록시를 통해 Site Recovery에 연결할 경우 프록시 서버가 특정 URL에 연결할 수 있어야 합니다.<br/><br/> [구체적인 배포 필수 조건](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment). | **주 사이트**: 프로세스 서버를 설정해야 합니다.<br/><br/> **장애 복구**: 물리적 서버를 복제하는 경우에도 Azure에서 장애 복구를 수행하려면 VMware 환경이 필요합니다. 온-프레미스 사이트에서 vContinuum 서버 및 마스터 대상 서버를 설정해야 합니다. Azure에서 프로세스 서버를 설정해야 합니다. [자세히 알아보기](site-recovery-failback-azure-to-vmware-classic-legacy.md)<br/><br/> **Azure 계정**: [Azure](https://azure.microsoft.com/) 계정 및 구독이 필요합니다.<br/><br/> **Azure 저장소**: 복제된 데이터를 저장하려면 [Azure 저장소 계정](../storage/storage-redundancy.md#geo-redundant-storage)이 있어야 합니다. 복제된 데이터는 Azure 저장소에 저장되고 장애 조치(Failover) 발생 시 Azure VM이 작동합니다.<br/><br/> **Azure 인프라 VM**: 구성 서버와 마스터 대상 서버를 Azure VM으로 설정해야 합니다.<br/><br/> **Azure 가상 네트워크**: 구성 서버와 마스터 대상 서버를 배포할 Azure 가상 네트워크가 필요합니다. 장애 조치(Failover) 후 Azure VM이 이 네트워크에 연결됩니다.<br/><br/> **보호된 컴퓨터**: VMware 가상 컴퓨터 또는 물리적 Windows/Linux 서버 하나 이상. 배포하는 동안 복제하려는 각 컴퓨터에 모바일 서비스가 설치됩니다.<br/><br/> **연결**: 관리 서버가 프록시를 통해 Site Recovery에 연결할 경우 프록시 서버가 특정 URL에 연결할 수 있어야 합니다.<br/><br/> [구체적인 배포 필수 조건](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start). | **주 사이트**: 전용 Windows 서버(물리적 컴퓨터 또는 VMware 가상 컴퓨터).<br/><br/> **보조 사이트**: 전용 구성 및 마스터 대상 서버.<br/><br/> **보호된 컴퓨터**: VMware 가상 컴퓨터 또는 물리적 Windows/Linux 서버 하나 이상. 배포하는 동안 통합 에이전트가 각 컴퓨터에 설치됩니다.





## Azure 가상 컴퓨터 요구 사항

Azure에서 지원하는 운영 체제를 실행하는 가상 컴퓨터와 물리적 서버를 복제하기 위해 사이트 복구를 배포할 수 있습니다. 여기에는 대부분 버전의 Windows 및 Linux가 포함됩니다. 보호할 온-프레미스 가상 컴퓨터가 Azure 요구 사항을 준수하는지 확인해야 합니다.


**기능** | **지원** | **세부 정보**
---|---|---
Hyper-V 호스트 운영 체제 | Windows Server 2012 R2 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
VMware 하이퍼바이저 운영 체제 | 지원되는 운영 체제 실행 | [세부 정보](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
게스트 운영 체제 | Hyper-V와 Azure 간 복제의 경우 사이트 복구에서는 [Azure에서 지원하는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 모든 운영 체제를 지원합니다. <br/><br/> VMware와 물리적 서버 간 복제의 경우 Windows 및 Linux [필수 구성 요소](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)를 확인하세요. | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
게스트 운영 체제 아키텍처 | 64비트 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
데이터 디스크 수 | 16 개 이하(최대 값은 만들고자 하는 가상 컴퓨터 크기의 함수) 16 = XL) | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
데이터 디스크 VHD 크기 | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
고정 IP 주소 | 지원됨 | 주 가상 컴퓨터가 고정 IP 주소를 사용하는 경우 Azure에서 생성되는 가상 컴퓨터에 대해 고정 IP 주소를 지정할 수 있습니다. Hyper-v에서 실행되는 Linux 가상 컴퓨터에 대한 고정 IP 주소는 지원되지 않습니다. 
iSCSI 디스크 | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
공유 VHD | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
FC 디스크 | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
하드 디스크 형식| VHD <br/><br/> VHDX | 현재 Azure에서는 VHDX가 지원되지 않지만 Azure로 장애 조치하면 사이트 복구가 자동으로 VHDX를 VHD로 변환합니다. 온-프레미스에 장애 복구 시 가상 컴퓨터에서 계속해서 VHDX 형식을 사용합니다.
가상 컴퓨터 이름| 1 자에서 63자 사이입니다. 문자, 숫자 및 하이픈으로 제한됩니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. | 사이트 복구에서 가상 컴퓨터 속성 내 값 업데이트
가상 컴퓨터 유형 | <p>1세대</p> <p>2세대 Windows</p> | 300GB보다 작은 VHDX의 디스크 형식인 데이터 볼륨 1 또는 2를 포함하는 기본 디스크의 OS 디스크 형식인 2세대 가상 컴퓨터입니다. Linux 2세대 가상 컴퓨터는 지원되지 않습니다. [자세한 정보](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## 배포 최적화

다음 팁을 사용하면 배포의 최적화 및 크기 조정에 도움이 됩니다.

- **운영 체제 볼륨 크기**: Azure에 가상 컴퓨터를 복제하는 경우 운영 체제 볼륨이 1TB 미만이어야 합니다. 이것보다 더 많은 볼륨이 있는 경우 배포를 시작하기 전에 볼륨을 다른 디스크로 수동으로 이동할 수 있습니다.
- **데이터 디스크 크기**: Azure에 복제하는 경우 최대 1TB의 각 가상 컴퓨터에서 최대 32 개의 데이터 디스크를 사용할 수 있습니다. ~32 TB 가상 컴퓨터를 효과적으로 복제 및 장애 조치할 수 있습니다.
- **복구 계획 제한**: 사이트 복구는 수천 개의 가상 컴퓨터로 확장할 수 있습니다. 복구 계획은 다 함께 장애 조치되어야 하는 응용 프로그램에 대한 모델로 설계되었으므로 복구 계획에서 컴퓨터의 수를 50으로 제한할 수 있습니다.
- **Azure 서비스 제한**: 모든 Azure 구독은 코어, 클라우드 서비스 등에서 일련의 기본 제한값이 제공됩니다. 구독에서 리소스의 가용성을 확인하기 위해 테스트 장애 조치를 실행하는 것이 좋습니다. Azure 지원을 통해 이러한 제한을 수정할 수 있습니다.
- **용량 계획**: Site Recovery에 대한 [용량 계획](site-recovery-capacity-planner.md)을 읽어 보세요.
- **복제 대역폭**: 복제 대역폭이 부족한 경우 다음에 유의하십시오.
	- **Express 경로**: 사이트 복구는 Riverbed와 같은 Azure Express 경로 및 WAN 최적화 프로그램과 함께 작동합니다. [여기서](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) Express 경로에 대해 더 알아볼 수 있습니다.
	- **복제 트래픽**: 사이트 복구는 전체 VHD가 아닌 데이터 블록만 사용하여 스마트 초기 복제를 수행합니다. 복제가 진행 중일 때는 변경 내용만 복제됩니다.
	- **네트워크 트래픽**:[Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)(대상 IP 주소 및 포트 기반 정책 포함) 또한 Azure 백업 에이전트를 사용하여 Azure Site Recovery에 복제하는 경우. 해당 에이전트에 대한 제한을 구성할 수 있습니다. [자세히 알아보기](https://support.microsoft.com/kb/3056159).
- **RTO**: 사이트 복구를 통해 예상할 수 있는 복제 시간 개체(RTO)를 측정하고 싶다면 테스트 장애 조치를 실행하고 작업 완료까지 시간이 얼마나 걸리는지 분석합니다. Azure에 장애 조치하는 경우 최선의 RTO를 위해 Azure 자동화 및 복구 계획과 통합하여 모든 수동 작업을 자동화하는 것이 좋습니다.
- **RPO**: Azure에 복제하는 경우 사이트 복구는 가까운 동기 복구 지점 목표 (RPO)를 지원 합니다. 이것은 데이터 센터와 Azure 사이에서 충분한 대역폭을 가정합니다.





## 다음 단계

일반적인 배포 요구 사항을 이해하고 비교한 후에는 구체적인 필수 조건을 읽고 각 시나리오 배포를 시작할 수 있습니다.

- [Azure에 VMWare 가상 컴퓨터 복제](site-recovery-vmware-to-azure-classic.md)
- [물리적 서버를 Azure에 복제](site-recovery-vmware-to-azure-classic.md)
- [VMM 클라우드의 Hyper-V 서버를 Azure로 복제](site-recovery-vmm-to-azure.md)
- [Hyper-V 가상 컴퓨터(VMM 없음)를 Azure로 복제](site-recovery-hyper-v-site-to-azure.md)
- [Hyper-V VM을 보조 사이트로 복제](site-recovery-vmm-to-vmm.md)
- [SAN을 사용하여 Hyper-V VM을 보조 사이트로 복제](site-recovery-vmm-san.md)
- [단일 VMM 서버를 사용하여 Hyper-V VM 복제](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0309_2016-->