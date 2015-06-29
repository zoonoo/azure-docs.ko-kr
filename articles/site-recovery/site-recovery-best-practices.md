<properties
	pageTitle="사이트 복구 배포에 대한 모범 사례"
	description="Azure Site Recovery는 온-프레미스 서버에 있는 가상 컴퓨터와 Azure 또는 보조 데이터센터 간의 복제, 장애 조치(Failover) 및 복구를 조정합니다."
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
	ms.date="05/08/2015"
	ms.author="raynew"/>

# 사이트 복구 배포에 대한 모범 사례


<a id="overview" name="overview" href="#overview"></a>

## 이 문서의 내용

이 문서에는 Azure 사이트 복구를 배포하기 전에 읽고 구현해야 하는 모범 사례가 포함되어 있습니다. 사이트 복구 및 관련 배포 시나리오에 대한 소개는 [사이트 복구 개요](site-recovery-overview.md)를 읽으십시오.

이 문서를 읽은 후 질문이 있다면 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시해 주십시오.


## Azure 준비

- **Azure 계정**: [Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. 없는 경우 [무료 평가판](pricing/free-trial/)으로 시작하세요.
- 사이트 복구에 대한 [가격 책정](pricing/details/site-recovery/)을 읽어보세요. 
- **Azure 저장소**: Azure로의 복제와 함께 사이트 복구를 배포하는 경우 Azure 저장소 계정이 필요합니다. 배포 중 하나를 설정하거나 시작하기 전에 하나를 준비할 수 있습니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다. [Microsoft Azure 저장소 소개](../storage/storage-introduction.md)를 읽으십시오. 

## 가상 컴퓨터

Azure 저장소에 복제하고자 한다면 다음을 참조하십시오.

- **지원되는 운영 체제**: 사이트 복구를 배포하여 Azure에서 지원하는 모든 운영 체제를 실행 중인 가상 컴퓨터의 보호를 오케스트레이션할 수 있습니다. 여기에는 대부분 버전의 Windows 및 Linux가 포함됩니다.
- **VHDX 지원**: 현재 Azure에서는 VHDX가 지원되지 않지만 Azure로 장애 조치하면 사이트 복구가 자동으로 VHDX를 VHD로 변환합니다. 온-프레미스에 장애 복구 시 가상 컴퓨터에서 계속해서 VHDX 형식을 사용합니다.
- **Azure 요구 사항**: 보호하고자 하는 가상 컴퓨터가 Azure 요구 사항을 준수하는지 확인하십시오.

**가상 컴퓨터 기능** | **지원** | **세부 정보**
---|---|---
호스트 운영 체제 | Windows Server 2012 R2 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
게스트 운영 체제 | <p>Windows Server 2008 R2 이상</p><p>Linux: Centos, openSUSE, SUSE, Ubuntu</p> | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. VMM 콘솔에서 값을 업데이트합니다.
게스트 운영 체제 아키텍처 | 64비트 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다. VMM 콘솔에서 값을 업데이트합니다.
데이터 디스크 수 | 16 개 이하(최대 값은 만들고자 하는 가상 컴퓨터 크기의 함수) 16 = XL) | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
데이터 디스크 VHD 크기 | 최대 1023GB | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
고정 IP 주소 | 지원됨 | 주 가상 컴퓨터가 고정 IP 주소를 사용하는 경우 Azure에서 생성되는 가상 컴퓨터에 대해 고정 IP 주소를 지정할 수 있습니다.
iSCSI 디스크 | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
공유 VHD | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
FC 디스크 | 지원되지 않음 | 지원되지 않는 경우 필수 구성 요소 확인이 실패합니다.
하드 디스크 형식| <p>VHD</p><p>VHDX</p> |
가상 컴퓨터 이름| 1 자에서 63자 사이입니다. 문자, 숫자 및 하이픈으로 제한됩니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. | 사이트 복구에서 가상 컴퓨터 속성 내 값 업데이트
가상 컴퓨터 유형 | <p>1세대</p> <p>2세대 Windows</p> | 300GB보다 작은 VHDX의 디스크 형식인 데이터 볼륨 1 또는 2를 포함하는 기본 디스크의 OS 디스크 형식인 2세대 가상 컴퓨터입니다. Linux 2세대 가상 컴퓨터는 지원되지 않습니다. [자세한 정보](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) 


## VMM 서버

사이트 복구는 다음과 같이 시스템 센터 가상 컴퓨터 관리자(VMM) 클라우드 내 Hyper-V 호스트 서버에 위치한 가상 컴퓨터에 대한 복제를 오케스트레이션할 수 있습니다. - 온 사이트 VMM 서버에서 Azure로 복제(Hyper-V 복제본 사용) - 보조 온-프레미스 사이트로 복제(Hyper-V 복제본 사용). 기본 사이트에서 VMM 서버 1개, 보조 사이트에서 1개와 함께 배포할 것을 권장합니다. 그러나 필요한 경우 두 사이트에 모두 [단일 VMM 서버를 배포](site-recovery-single-vmm.md)할 수 있습니다. - 보조 온-프레미스 사이트로 복제(SAN 사용) 각 사이트에 VMM 서버 1개가 있는 기본 및 보조 데이터 센터가 필요합니다. 사이트 복구를 사용하여 VMM을 배포하려면 VMM 인프라를 설정해야 합니다. VMM 서버가 없다면 [여기서](site-recovery-hyper-v-site-to-azure.md) 더 읽으십시오.


### VMM 버전 확인

**지원되는 VMM 버전** | **지원되는 사이트 복구 시나리오** | **지원되는 Hyper-V 호스트 (소스/대상)** | **지원되는 공급자/에이전트**
---|---|---|---
최소 업데이트 롤업 5가 있는 System Center 2012 R2 상의 VMM(클러스터에서만 가상) | SAN 복제를 사용한 온-프레미스 대 온-프레미스 보호 | 최신 업데이트가 포함된 최소 Windows Server 2012 | 최신 버전의 Azure Site Recovery 공급자(VMM 서버에 설치) |
System Center 2012 R2에서 VMM(권장) (클러스터 또는 독립 실행형) | <p>Hyper-v 복제를 사용한 온-프레미스 대 온-프레미스 보호</p> <p>Hyper-v 복제를 사용한 온-프레미스 Azure 보호</p> | <p>최신 업데이트 포함 최소 Windows Server 2012</p><p>Windows Server 2012 R2(원본, 대상에 적용되지 않음)</p> | <p>최신 버전의 Azure Site Recovery 공급자(VMM 서버에 설치)</p> <p>최신 버전의 Azure 복구 서비스 에이전트(Azure에 전용 복제를 위해 Hyper-v 호스트 서버에 설치)</p>
최신 누적 업데이트 포함 System Center 2012 SP1에서 VMM(클러스터 또는 독립 실행형) | 온-프레미스 대 온-프레미스 보호 | 최신 업데이트 포함 Windows Server 2012 | 최신 버전의 Azure Site Recovery 공급자(VMM 서버에 설치) |

### VMM 클라우드 인프라 설정

사이트 복구로 어떤 VMM 시나리오를 배포하든 상관 없이 VMM에 최소 2개의 사설 클라우드를 생성해야(하나는 원본 VMM 서버에, 하나는 대상 서버에) VMM 콘솔을 사용하여 클라우드 및 기본 리소스를 정의, 관리 및 액세스하면서 사설 클라우드의 이점을 얻을 수 있습니다. VMM 클라우드는 클라우드 용량 설정 및 사용자 역할 할당량에 의해 사용량이 제한된 리소스의 집계 집합을 수집 및 표시합니다. 셀프 서비스 사용자는 기본 인프라를 완전히 이해하지 않고도 클라우드의 리소스를 관리 및 사용할 수 있습니다. 클라우드의 탄력성을 축소 또는 확장하기 위해 리소스를 쉽게 추가할 수 있습니다. 클라우드를 설정해야 하는 경우 다음 리소스를 사용합니다.


- [System Center 2012 R2 VMM을 사용하는 사설 클라우드의 새로운 기능](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=)
- [VMM 2012 및 클라우드](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
- [VMM 클라우드 패브릭 구성](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
- [VMM에서 사설 클라우드 만들기](https://technet.microsoft.com/ko-kr/library/jj860425.aspx)
- [연습: System Center 2012 SP1 VMM을 사용하여 사설 클라우드 만들기](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).




## 공급자 및 에이전트

공급자와 에이전트가 온-프레미스 서버에 설치되므로 Azure 내의 사이트 복구에 연결할 수 있습니다. 암호화된 HTTPS 연결을 사용하여 인터넷을 통해 연결합니다.

- **공급자 요구 사항**: 배포 중에 온-프레미스 서버에서 몇가지 구성 요소를 설치 합니다.
	- **VMM 서버에서**: 자격 증명 모음에 등록하려는 VMM 서버에 Azure Site Recovery 공급자를 설치합니다.
	- **VMM 클라우드에 위치한 Hyper-V 호스트 서버에서**: Azure 복구 서비스 에이전트를 설치합니다.
	- **VMM 서버가 없는 Hyper-V 호스트 서버**: 각 Hyper-V 호스트 서버나 클러스터 노드에서 Azure Site Recovery 공급자와 Azure 복구 서비스 에이전트를 설치합니다.
-  **공급자 설치**: 공급자와 에이전트를 설치할 때 다음을 유의하십시오.
	-  사이트 복구 자격 증명 모음 내 모든 서버에 동일한 버전의 공급자를 설치해야 합니다. 단일 자격 증명 모음 내에서 서로 다른 버전을 사용하는 것은 지원되지 않습니다.
	-  VMM 클라우드에 있는 Hyper-V 서버로부터 Azure로 복제하려면 VMM 서버가 System Center 2012 R2를 실행해야 합니다. 보조 데이터 센터에 복제하려면 VMM이 SP1 또는 R2가 설치된 System Center 2012를 실행해야 합니다.
- **프록시**: 방화벽 예외를 추가하거나 특정 프록시를 만들지 않아도 됩니다. 공급자 연결에 대해 사용자 지정 프록시를 사용하지 않으려면 배포를 시작하기 전에 다음을 수행해야 합니다.

	- 공급자 설치 전에 사용자 지정 프록시 서버를 설정합니다.
	- 방화벽을 통해 다음 URL을 허용:
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net 
	- VMM으로 사이트 복구를 배포하고 사용자 지정 프록시를 사용하는 경우 VMM RunAs 계정 (DRAProxyAccount)이 사이트 복구 포털 내 사용자 지정 프록시 설정에서 지정한 프록시 자격 증명을 사용하여 자동으로 생성됩니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 설정해야 합니다.



## 온-프레미스 연결

- **공급자 연결**: 공급자와 에이전트가 온-프레미스 서버에 설치되므로 사이트 복구에 연결할 수 있습니다.. 암호화된 HTTPS 연결을 사용하여 인터넷을 통해 사이트 복구에 연결합니다. 방화벽 예외를 추가하거나 특정 프록시를 만들지 않아도 됩니다.
- **인터넷 연결**: 다음과 같은 서버 인터넷 연결이 필요합니다.
	- VMM 클라우드 내 Hyper-V 호스트 서버에서 가상 컴퓨터를 보호하려는 경우 VMM 서버만 인터넷에 연결해야 합니다.
	- VMM 서버 없이 Hyper-V 호스트 서버에서 가상 컴퓨터를 보호하려는 경우 Hyper-V 호스트 서버만 인터넷에 연결해야 합니다.
	- 보호하려는 가상 컴퓨터에서는 인터넷 연결이 필요하지 않습니다.
- **VPN 사이트 간**: 사이트 복구에 연결하기 위해 VPN 사이트 간 연결이 필요한 것은 아닙니다. 그러나 사이트 간 연결이 있는 경우에는 장애 조치 전에 했던 것처럼 장애 조치가 취해진 가상 컴퓨터에 액세스할 수 있습니다. Azure로 복제할 때는 온-프레미스 사이트와 특정 Azure 네트워크 사이에 VPN 사이트 간 네트워크를 설정하게 됨을 기억하십시오. 암호화된 복제 트래픽에 사용되지 않습니다. 인터넷을 통해 구독 내 Azure 저장소 계정으로 이동합니다.
- **장애 조치 후 연결**: 사용자가 다음을 수행하는 Azure에 장애 조치 후 가상 컴퓨터에 연결할 수 있는지 확인하려면:
	- 장애 조치 전에 가상 컴퓨터에 대해 RDP를 활성화합니다.
	- 장애 조치 후 사이트 간 연결을 사용하여 이전에 수행한 것처럼 연결할 수 있게 하거나 컴퓨터에 대해 RDP 끝점을 활성화합니다.

## 저장소

- **Azure 저장소 계정**: Azure에 복제하면 Azure 저장소 계정이 필요합니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다. 자세한 내용은 [Microsoft Azure 저장소 소개](../storage/storage-introduction.md)를 참조합니다.
- **저장소 매핑**: 온-프레미스 VMM 서버에 가상 컴퓨터를 복제하는 경우, 장애 조치 후 가상 컴퓨터가 저장소에 최적으로 연결되도록 저장소 매핑을 설정할 수 있습니다. 2개의 온-프레미스 VMM 사이트 사이에서 복제하는 경우 기본적으로 복제본 가상 컴퓨터가 대상 Hyper-V 호스트 서버 상에서 표시된 곳에 저장됩니다. 원본 VMM 서버와 대상 VMM 서버에서 VMM 저장소 분류 사이에 매핑을 구성할 수 있습니다. 이 기능을 사용하려면 배포를 시작하기 전에 저장소 분류를 설정하십시오. [여기서](site-recovery-storage-mapping.md) 저장소 매핑에 대해 더 알아볼 수 있습니다.
- **SAN**: SAN 복제와 함께 두 개의 온-프레미스 사이트 간에 복제하려는 경우 다음을 유의하십시오.
	- SAN 복제를 사용하면 Hyper-V 가상 컴퓨터를 보조 데이터 센터에만 복제할 수 있습니다. Azure에는 복제할 수 없습니다.
	- 기존 SAN 환경을 사용할 수 있습니다. SAN 배열을 변경할 필요가 없습니다.
	- 자신의 SAN 배열이 [지원되는지](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) 확인해야 합니다.
	- SAN 배포를 위해서는 원본 및 대상 사이트에 두 개의 VMM 서버가 필요합니다.
	- SAN 복제를 배포하려는 경우 Hyper-V 호스트 클러스터가 Windows Server 2012 또는 2012 R2를 실행하고 있어야 합니다. [여기서](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 다양한 버전의 Hyper-V에 의해 지원되는 게스트 운영 체제를 볼 수 있습니다.
	- VMM에서 SAN 저장소를 발견 및 분류해야 합니다.
	- 아직 복제하지 않는 경우 발견 후에 LUN을 생성하고 VMM 콘솔에서 저장소를 할당해야 합니다. 이미 복제하고 있다면 이 단계를 건너뛰어도 됩니다.
	- 전체 지침은 이 [문서](site-recovery-vmm-san.md)에서 볼 수 있습니다.


## 네트워크

- **네트워킹 고려 사항에 대해 알아보기**: [여기서](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) 네트워킹 고려 사항 및 모범 사례를 확인할 수 있습니다.

- **네트워크 매핑 설정**: VMM 및 사이트 복구를 배포할 때는 네트워크 매핑이 중요한 요소입니다. 대상 Hyper-V 호스트 서버에 복제된 가상 컴퓨터를 최적으로 배치하고 장애 조치(Failover) 후 복제된 가상 컴퓨터가 적합한 네트워크에 연결되도록 합니다. Azure 또는 보조 데이터 센터에 복제 하는 경우 네트워크 매핑을 구성할 수 있습니다.
	- **Azure로 네트워킹 매핑**: Azure네트워크 매핑으로 복제하는 경우 동일한 네트워크에서 장애 조치된 모든 컴퓨터가 자신이 속해있는 복구 계획에 상관 없이 서로 연결할 수 있도록 해야 합니다. 또한, 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정된 경우 가상 컴퓨터가 다른 온-프레미스 가상 컴퓨터에 연결할 수 있습니다. 동일한 복구 계획에서 장애 조치되는 네트워크 매핑 컴퓨터를 설정하지 않는 경우 연결 가능합니다.
	- **보조 사이트에 네트워크 매핑**: 보조 VMM 사이트 네트워크 매핑에 복제하는 경우 장애 조치 후에 가상 컴퓨터가 적절한 네트워크에 연결되어 있고 복제본 가상 컴퓨터가 Hyper-v 호스트 서버에 최적으로 배치되도록 하십시오. 네트워크 매핑을 구성하지 않으면 복제된 컴퓨터가 어떤 VM 네트워크에도 연결되지 않습니다.
	- [여기서](site-recovery-network-mapping) 네트워크 매핑에 대해 자세히 알아봅니다.
- **VMM 네트워크 설정**:
	- VMM에서 논리와 VM 네트워크를 올바르게 구성합니다. [논리 네트워크](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) 및 [VM 네트워크](https://technet.microsoft.com/library/jj721575.aspx)에 대해 알아봅니다.
	- 원본 VMM 서버에서 모든 가상 컴퓨터가 VM 네트워크에 연결되도록 하십시오.
	- VM 네트워크가 해당 클라우드와 연관된 논리 네트워크에 연결되어야 합니다.
	- Azure에 복제하는 경우 Azure에서 가상 네트워크를 만듭니다. 단일 Azure 네트워크에 여러 개의 VM 네트워크를 매핑할 수 있습니다. [가상 네트워크 구성 작업](https://msdn.microsoft.com/library/azure/dn133795.aspx)에 대해 알아봅니다.

## 성능 최적화

- **운영 체제 볼륨 크기**: Azure에 가상 컴퓨터를 복제하는 경우 운영 체제 볼륨이 127GB 미만이어야 합니다. 이것보다 더 많은 볼륨이 있는 경우 배포를 시작하기 전에 볼륨을 다른 디스크로 수동으로 이동할 수 있습니다.
- **데이터 디스크 크기**: Azure에 복제하는 경우 최대 1TB의 각 가상 컴퓨터에서 최대 32 개의 데이터 디스크를 사용할 수 있습니다. ~32 TB 가상 컴퓨터를 효과적으로 복제 및 장애 조치할 수 있습니다.
- **복구 계획 제한**: 사이트 복구는 수천 개의 가상 컴퓨터로 확장할 수 있습니다. 복구 계획은 다 함께 장애 조치되어야 하는 응용프로그램에 대한 모델로 설계되었으므로 복구 계획에서 컴퓨터의 수를 50으로 제한할 수 있습니다.
- **Azure 서비스 제한**: 모든 Azure 구독은 코어, 클라우드 서비스 등에서 일련의 기본 제한값이 제공됩니다. 구독에서 리소스의 가용성을 확인하기 위해 테스트 장애 조치를 실행하는 것이 좋습니다. Azure 지원을 통해 이러한 제한을 수정할 수 있습니다.
- **용량 계획**: 지침은 [Hyper-v 복제본을 위한 용량 플래너](http://www.microsoft.com/ko-kr/download/details.aspx?id=39057)를 참조하십시오.
- **복제 대역폭**: 복제 대역폭이 부족한 경우 다음에 유의하십시오.
	- **Express 경로**: 사이트 복구는 Riverbed와 같은 Azure Express 경로 및 WAN 최적화 프로그램과 함께 작동합니다. [여기서](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) Express 경로에 대해 더 알아볼 수 있습니다.
	- **복제 트래픽**: 사이트 복구는 전체 VHD가 아닌 데이터 블록만 사용하여 스마트 초기 복제를 수행합니다. 복제가 진행 중일 때는 변경 내용만 복제됩니다.
	- **네트워크 트래픽**:[Windows QoS](https://technet.microsoft.com/library/hh967468.aspx)(대상 IP 주소 및 포트 기반 정책 포함) 또한 Azure 백업 에이전트를 사용하여 Azure Site Recovery에 복제하는 경우. 해당 에이전트에 대한 제한을 구성할 수 있습니다. [자세히 알아보기](https://support.microsoft.com/kb/3056159).
- **RTO**: 사이트 복구를 통해 예상할 수 있는 복제 시간 개체(RTO)를 측정하고 싶다면 테스트 장애 조치를 실행하고 작업 완료까지 시간이 얼마나 걸리는지 분석합니다. Azure에 장애 조치하는 경우 최선의 RTO를 위해 Azure 자동화 및 복구 계획과 통합하여 모든 수동 작업을 자동화하는 것이 좋습니다.
- **RPO**: Azure에 복제하는 경우 사이트 복구는 가까운 동기 복구 지점 목표 (RPO)를 지원 합니다. 이것은 데이터 센터와 Azure 사이에서 충분한 대역폭을 가정합니다.

## 장애 조치
- **기본 사이트에서 중단**: 하나의 온-프레미스 데이터 센터에서 다른 데이터 센터 및 양쪽 데이터 센터 모두에 복제하려고 하는데 기본 사이트에서 중단을 경험한다면 사이트 복구 포털에서 계획되지 않은 장애 조치를 실행합니다. 장애 조치를 실행하기 위해 기본 데이터 센터의 연결은 필요 없습니다.
- **보조 사이트로 장애 조치 후 IP 주소 유지**: 보조 데이터 센터에 장애 조치 후 원본 가상 컴퓨터의 IP 주소를 유지하고자 한다면 [여기](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx)에서 다음 단계를 따르십시오.
- **Azure에 장애 조치 후 IP 주소 유지**: 가상 컴퓨터의 구성 탭에서 VM 장애 조치를 위해 제공할 IP를 지정할 수 있습니다. 자세한 내용은 [가상 컴퓨터의 네트워크 속성 구성](site-recovery-vmm-to-azure.md#step-8-enable-protection-for-virtual-machines) 방법을 참조하십시오.
- **공용 IP 주소 유지**: 보조 사이트로 장애 조치 후에 공용 IP 주소를 유지하려는 경우 ISP에서 지원한다면 사이트 복구는 이것을 금지하지 않습니다. Azure에 장애 조치 후 공용 IP 주소를 유지할 수 없습니다.
- **Azure에서 RFC가 아닌 내부 주소 유지**: Azure에 장애 조치 후 RFC 1918이 아닌 주소 공간을 유지할 수 있습니다.
- **보조 데이터 센터에 부분 장애 조치**: 보조 데이터 센터에 부분 사이트를 장애 조치하고 기본 사이트에 다시 연결하고자 한다면 사이트 간 VPN을 사용하여 보조 사이트에서 장애 조치한 응용프로그램을 기본 사이트에서 실행 중인 인프라 구성 요소에 연결할 수 있습니다. 전체 서브넷을 장애 조치할 경우 가상 컴퓨터 IP 주소를 유지할 수 있음을 기억하십시오. 부분 서브넷을 장애 조치할 경우 서브넷을 사이트 사이에서 분할할 수 없으므로 가상 컴퓨터 IP 주소를 유지할 수 없습니다.
- **Azure에 부분 장애 조치**: Azure에 부분 사이트를 장애 조치하고 기본 사이트에 다시 연결하고자 한다면 사이트 간 VPN을 사용하여 Azure에서 장애 조치한 응용프로그램을 기본 사이트에서 실행 중인 인프라 구성 요소에 연결할 수 있습니다. 전체 서브넷을 장애 조치할 경우 가상 컴퓨터 IP 주소를 유지할 수 있음을 기억하십시오. 부분 서브넷을 장애 조치할 경우 서브넷을 사이트 사이에서 분할할 수 없으므로 가상 컴퓨터 IP 주소를 유지할 수 없습니다.
- **드라이브 문자 유지**: 장애 조치 후 가상 컴퓨터에서 드라이브 문자를 유지하려는 경우 가상 컴퓨터에 대한 SAN 정책을 **On**으로 설정할 수 있습니다. [자세히 알아보기](https://support.microsoft.com/kb/3031135).
- **Azure에 장애 조치 후 클라이언트 요청을 라우팅**: 장애 조치 후 응용프로그램에 대 한 클라이언트 요청 경로 Azure 트래픽 관리자와 함께 작동 하는 사이트 복구 합니다. 복구 계획에서 스크립트를 사용하여(Azure 자동화와 함께) DNS 업데이트를 수행할 수 있습니다.

## 통합

- **다른 BCDR 기술과 통합**: 사이트 복구는 다른 비즈니스 연속성 및 재해 복구 (BCDR) 기술을 통합합니다. 응용프로그램 기반 복제의 경우 SQL Sever AlwaysOn을 사용하여 데이터베이스를 실행 중인 가상 컴퓨터를 복제하고 프런트엔드 가상 컴퓨터에는 Hyper-v 복제본을 실행할 것을 권장합니다. AlwaysOn은 기본 및 보조 사이트에 SQL Server Enterprise 라이선스를 필요로 하며, 구독에서 Azure 가상 컴퓨터를 실행해야 합니다. 응용프로그램이 일부 가동 중지 시간을 처리할 수 있는 경우에는 sing 사이트 복구 내장형 복제가 유용하며, 이 경우 사이트 복구를 사용하여 데이터베이스, 응용프로그램 및 프런트엔드 가상 컴퓨터의 복제를 오케스트레이션할 것을 권장합니다. 이 방법을 사용해야 필요한 SQL Server 버전, 라이선스 수 및 항상 Azure에서 실행 중인 가상 컴퓨터의 유지비를 절약할 수 있습니다.

## 다음 단계

이러한 모범 사례를 검토한 후 사이트 복구의 배포를 시작할 수 있습니다.

- [온-프레미스 VMM 사이트와 Azure 간 보호 설정](site-recovery-vmm-to-azure.md)
- [온-프레미스 Hyper-V 사이트와 Azure 간 보호 설정](site-recovery-hyper-v-site-to-azure.md)
- [2개의 온-프레미스 VMM 사이트 간 보호 설정](site-recovery-vmm-to-vmm.md)
- [SAN으로 2개의 온-프레미스 VMM 사이트 간 보호 설정](site-recovery-vmm-san.md)
- [단일 VMM 서버로 보호 설정](site-recovery-single-vmm.md)
 

<!---HONumber=58_postMigration-->