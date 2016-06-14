<properties
	pageTitle="Azure 포털을 사용하여 보조 VMM 사이트에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제 | Microsoft Azure"
	description="Azure Site Recovery를 배포하고 Azure 포털을 사용하여 VMM 클라우드의 Hyper-V VM을 보조 VMM 사이트에 복제, 장애 조치(Failover) 및 복구하는 작업을 어떻게 조정해야 하는지 설명합니다."
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
	ms.date="05/26/2016"
	ms.author="raynew"/>

# Azure 포털을 사용하여 보조 VMM 사이트에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제

> [AZURE.SELECTOR]
- [Azure 포털](site-recovery-vmm-to-vmm.md)
- [클래식 포털](site-recovery-vmm-to-vmm-classic.md)

Azure Site Recovery에 오신 것을 환영합니다! Azure 포털을 통해 System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 온-프레미스 Hyper-V 가상 컴퓨터를 보조 사이트에 복제하려는 경우 이 문서를 사용하세요. 이 문서는 Azure 포털의 Azure Site Recovery를 사용하여 복제를 설정하는 방법을 설명합니다.

> [AZURE.NOTE] Azure는 리소스를 만들고 작업하기 위한 두 가지 [배포 모델](../resource-manager-deployment-model.md)로 ARM(Azure Resource Manager)과 클래식을 제공합니다. 또한 Azure에는 두 가지 포털이 있는데, 하나는 클래식 배포 모델을 지원하는 Azure 클래식 포털이고 다른 하나는 두 가지 배포 모델을 모두 지원하는 Azure 포털입니다.


Azure 포털의 Azure Site Recovery는 여러 새 기능을 제공합니다.

- Azure 포털에는 Azure 백업 및 Azure Site Recovery 서비스가 단일 복구 서비스 자격 증명 모음으로 결합되어 있어서 단일 위치에서 BCDR(비즈니스 연속성 및 재해 복구)을 설정하고 관리할 수 있습니다. 통합 대시보드를 통해 온-프레미스 사이트와 Azure 공용 클라우드의 작업을 모니터링하고 관리할 수 있습니다.
- CSP(클라우드 솔루션 공급자) 프로그램이 포함된 Azure 구독이 있는 사용자는 이제 Azure 포털에서 Site Recovery 작업을 관리할 수 있습니다.


이 문서를 읽은 후 하단의 Disqus 의견에 의견을 남겨 주세요. 기술 관련 문의 사항은 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 문의하세요.


## 개요

조직에서는 계획된 중단 또는 불의의 중지 시간에 앱, 워크로드 및 데이터를 실행 중이고 가용 상태로 유지하고 가능한 신속히 정상적인 작업 상태로 복귀하기 위한 BCDR 전략이 필요합니다. BCDR 전략은 재해가 발생했을 때 비즈니스 데이터를 안전하고 복구 가능하게 유지하고 워크로드를 지속적으로 가용 상태로 유지해야 합니다.

사이트 복구는 온-프레미스 물리적 서버와 가상 컴퓨터를 클라우드(Azure) 또는 보조 데이터센터에 복제하는 것을 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 기본 위치에서 중단이 발생하면 보조 위치로 장애 조치하여 앱과 워크로드를 가용 상태로 유지합니다. 기본 위치가 정상 작업 상태로 돌아오면 다시 기본 위치로 돌아갑니다. [Azure Site Recovery란?](site-recovery-overview.md)에서 자세한 내용을 확인해 보세요.

이 문서에서는 VMM 클라우드의 온-프레미스 Hyper-V VM을 보조 VMM 사이트에 복제하는 데 필요한 모든 정보를 제공합니다. 온-프레미스 서버, 복제 설정 및 용량 계획을 구성하기 위한 아키텍처 개요, 계획 정보 및 배포 단계가 포함되어 있습니다. 인프라를 설정한 후에는 보호하려는 컴퓨터에서 복제를 활성화하고 장애 조치(Failover)가 작동하는지 확인할 수 있습니다.

## 비즈니스 이점

- Site Recovery는 Hyper-V VM에서 실행되는 비즈니스 워크로드 및 응용 프로그램을 보조 Hyper-V 서버에 복제하여 보호를 제공합니다.
- 복구 서비스 포털은 복제, 장애 조치(Failover) 및 복구를 설정, 관리 및 모니터링하는 단일 위치를 제공합니다.
- 기본 온-프레미스 인프라에서 보조 사이트로의 장애 조치(Failover)와 보조 사이트에서 기본 사이트로의 대체(복원)를 쉽게 실행할 수 있습니다.
- 계층화된 응용 프로그램 워크로드가 함께 장애 조치(Failover)되도록 여러 컴퓨터로 복구 계획을 구성할 수 있습니다. 

## 시나리오 아키텍처

다음은 시나리오 구성 요소입니다.

- **기본 사이트**: 기본 사이트에는 복제하려는 원본 VM을 실행하는 하나 이상의 Hyper-V 호스트 서버가 있습니다. 이러한 기본 호스트 서버는 VMM 사설 클라우드에 있습니다.
- **보조 사이트**: 보조 사이트에는 기본 VM을 복제할 대상 VM을 실행하는 하나 이상의 Hyper-V 호스트 서버가 있습니다. 이러한 호스트 서버는 VMM 사설 클라우드에 있습니다. 클라우드는 기본 서버(VMM 서버가 하나만 있는 경우) 또는 보조 VMM 서버에 있을 수 있습니다.
- **공급자**: Site Recovery 배포 동안 VMM 서버에 Azure Site Recovery 공급자를 설치하고 해당 서버를 복구 서비스 자격 증명 모음에 등록하게 됩니다. VMM 서버에서 실행되는 공급자는 HTTPS 443을 통해 Site Recovery와 통신하여 오케스트레이션을 복제합니다. 기본 및 보조 Hyper-V 호스트 서버 간에 데이터 복제가 발생합니다. 복제된 데이터는 온-프레미스 사이트와 네트워크 내에 유지되며 Azure에 전송되지 않습니다. [개인 정보](#privacy-information-for-site-recovery)에 대해 자세히 알아보세요.

![E2E 토폴로지](./media/site-recovery-vmm-to-vmm/architecture.png)

### 데이터 프라이버시 개요

이 표는 이 시나리오에서 데이터가 저장되는 방법을 보여 줍니다.
****
작업 | **세부 정보** | **수집된 데이터** | **사용** | **필수** 
--- | --- | --- | --- | ---
**등록** | 복구 서비스 자격 증명 모음에 VMM 서버를 등록합니다. 나중에 서버를 등록 취소하려면 Azure 포털에서 서버 정보를 삭제하면 됩니다. | VMM 서버가 등록되면 Site Recovery는 VMM 서버에 대한 메타데이터와 Site Recovery에서 검색한 VMM 클라우드의 이름을 수집하고 처리하고 전송합니다. | 이러한 데이터는 해당 VMM 서버를 식별하고 통신하며 적절한 VMM 클라우드에 대한 설정을 구성하는 데 사용됩니다. | 이 기능은 필수입니다. Site Recovery에 이 정보를 보내지 않으려는 경우 Site Recovery 서비스를 사용하면 안 됩니다. 
**복제 활성화** | Azure Site Recovery 공급자는 VMM 서버에 설치되며 Site Recovery 서비스와 통신하기 위한 통로가 됩니다. 공급자는 VMM 프로세스에 호스트된 DLL(동적 연결 라이브러리)입니다. 공급자를 설치하면 VMM 관리자 콘솔에서 "데이터 센터 복구" 기능을 사용할 수 있습니다. 새 VM 및 기존 VM은 VM을 보호하기 위해 이 기능을 사용하도록 설정할 수 있습니다. | 이 속성이 설정되면 공급자는 VM의 이름과 ID를 Site Recovery로 보냅니다. Windows Server 2012 또는 Windows Server 2012 R2 Hyper-V 복제본을 통해 복제를 사용할 수 있습니다. 일반적으로 서로 다른 "복구" 데이터 센터에 있는 Hyper-V 호스트 간에 가상 컴퓨터 데이터가 복제됩니다. | Site Recovery는 메타데이터를 사용하여 Azure 포털에서 VM 정보를 채웁니다. | 이 기능은 서비스의 핵심 부분이며 해제할 수 없습니다. 이 정보를 보내지 않으려면 VM에 Site Recovery 보호를 사용하지 마세요. 공급자가 Site Recovery로 보내는 모든 데이터는 HTTPS를 통해 전송됩니다.
**복구 계획** | 복구 계획은 복구 데이터 센터에 대한 오케스트레이션 계획을 작성하는 데 도움이 됩니다. 복구 사이트에서 VM 또는 가상 컴퓨터 그룹이 시작되는 순서를 정의할 수 있습니다. 각 VM을 복구할 때 실행할 자동화된 스크립트나 수행할 수동 작업을 지정할 수도 있습니다. 장애 조치(Failover)는 일반적으로 조정된 복구에 대한 복구 계획 수준에서 트리거됩니다. | Site Recovery는 가상 컴퓨터 메타데이터, 자동화 스크립트 및 수동 작업 노트의 메타데이터 등을 포함하여 복구 계획에 대한 메타데이터를 수집, 처리 및 전송합니다. | 이 메타데이터는 Azure 포털에서 복구 계획을 작성하는 데 사용됩니다. | 이 기능은 서비스의 핵심 부분이며 해제할 수 없습니다. Site Recovery에 이 정보를 보내지 않으려는 경우 복구 계획을 만들지 마세요.
**네트워크 매핑** | 기본 데이터 센터에서 복구 데이터 센터로 네트워크 정보가 매핑됩니다. 복구 사이트에서 VM을 복구할 때 네트워크 매핑은 네트워크 연결을 설정하는 데 도움이 됩니다. | Site Recovery는 각 사이트(기본 사이트 및 데이터 센터)에 대한 논리적 네트워크의 메타데이터를 수집, 처리 및 전송합니다. | 메타데이터는 네트워크 정보를 매핑할 수 있도록 네트워크 설정을 채우는 데 사용됩니다. | 이 기능은 서비스의 핵심 부분이며 해제할 수 없습니다. Site Recovery에 이 정보를 보내지 않으려는 경우 네트워크 매핑을 사용하지 마세요.
**장애 조치(계획됨/계획되지 않음/테스트)** | 장애 조치(Failover)는 하나의 VMM 관리 데이터 센터에서 다른 데이터 센터로 VM을 장애 조치합니다. 장애 조치(Failover) 작업은 Azure 포털에서 수동으로 트리거됩니다. | VMM 서버의 공급자는 Site Recovery에서 장애 조치(Failover) 이벤트에 대한 알림을 받고 VMM 인터페이스를 통해 Hyper-V 호스트에서 장애 조치(Failover) 작업을 실행합니다. VM의 실제 장애 조치(Failover)는 한 Hyper-V 호스트에서 다른 호스트로 수행되고, Windows Server 2012 또는 Windows Server 2012 R2 Hyper-V 복제본에 의해 처리됩니다. 장애 조치(Failover)가 완료되면 복구 데이터 센터의 VMM 서버에 있는 공급자가 성공 정보를 Site Recovery에 보냅니다. | Site Recovery는 전송된 정보를 사용하여 Azure 포털에 장애 조치(Failover) 작업 상태 정보를 채웁니다. | 이 기능은 서비스의 핵심 부분이며 해제할 수 없습니다. Site Recovery에 이 정보를 보내지 않으려는 경우 장애 조치(Failover)를 사용하지 마세요.


## Azure 필수 조건

이 시나리오를 배포하려면 Azure에서 다음이 필요합니다.

**필수 구성 요소** | **세부 정보** 
--- | ---
**Azure**| [Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. Site Recovery 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/site-recovery/). 


## 온-프레미스 필수 조건

이 시나리오를 배포하기 위해 기본 및 보조 온-프레미스 사이트에서 필요한 사항은 다음과 같습니다.

**필수 구성 요소** | **세부 정보** 
--- | ---
**VMM** | 기본 사이트에서 VMM 서버 1개, 보조 사이트에서 VMM 서버 1개를 배포할 것을 권장합니다.<br/><br/> [단일 VMM 서버의 클라우드 간에 복제](site-recovery-single-vmm.md)할 수도 있습니다. 이렇게 하려면 VMM 서버에 둘 이상의 클라우드가 구성되어 있어야 합니다.<br/><br/> VMM 서버는 최신 업데이트를 설치한 System Center 2012 SP1 이상을 실행해야 합니다.<br/><br/> 각 VMM 서버에는 하나 이상의 클라우드가 구성되어 있어야 하고 모든 클라우드에 Hyper-V 용량 프로필이 설정되어 있어야 합니다. <br/><br/>클라우드에는 하나 이상의 VMM 호스트 그룹이 있어야 합니다.<br/><br/>[VMM 패브릭 클라우드 패브릭 구성](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) 및 [연습: System Center 2012 SP1 VMM으로 사설 클라우드 만들기](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)에서 VMM 클라우드 설정에 대해 자세히 알아봅니다.<br/><br/> VMM 서버는 인터넷에 연결되어야 합니다. 
**Hyper-V** | Hyper-V 서버는 Hyper-V 역할로 Windows Server 2012 이상을 실행해야 하며 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> Hyper-V 서버에 VM이 하나 이상 있어야 합니다.<br/><br/> Hyper-V 호스트 서버는 기본 및 보조 VMM 클라우드의 호스트 그룹에 있어야 합니다.<br/><br/> Windows Server 2012 R2에서 클러스터의 Hyper-V를 실행하는 경우 [업데이트 2961977](https://support.microsoft.com/kb/2961977)<br/><br/>을 설치해야 합니다. Windows Server 2012에서 클러스터의 Hyper-V를 실행하는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성해야 합니다. [자세히 알아보기](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**공급자** | 사이트 복구 배포 중에 VMM 서버에 Azure Site Recovery 공급자를 설치합니다. 공급자는 HTTPS 443을 통해 Site Recovery와 통신하여 복제를 오케스트레이션합니다. LAN 또는 VPN 연결을 통해 기본 및 보조 Hyper-V 서버 간에 데이터 복제가 발생합니다.<br/><br/> VMM 서버에서 실행되는 공급자는 다음 URL에 액세스할 수 있어야 합니다. *.hypervrecoverymanager.windowsazure.com, *.accesscontrol.windows.net, *.backup.windowsazure.com, *.blob.core.windows.net, *.store.core.windows.net<br/><br/> 또한 VMM 서버에서 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)로의 방화벽 통신을 허용하고 HTTPS(433) 프로토콜을 허용합니다.

## 배포 준비

배포를 준비하기 위해 다음을 수행해야 합니다.

1. Site Recovery를 배포할 [VMM 서버를 준비](#prepare-the-vmm-server)합니다. 
2. [네트워크 매핑을 준비](#prepare-for-network-mapping)합니다. 네트워크 매핑을 구성할 수 있도록 네트워크를 설정합니다.

### VMM 서버 준비

VMM 서버가 [필수 조건](#on-premises-prerequisites)을 준수하는지와 나열된 URL에 액세스할 수 있는지 확인합니다.


### 네트워크 매핑 준비

네트워크 매핑 기능은 다음을 위해 기본 및 보조 VMM 서버의 VMM VM 네트워크 간을 매핑합니다.

- 장애 조치(Failover) 후 보조 Hyper-V 호스트에 복제본 VM을 최적으로 배치합니다.
- 복제본 VM을 해당 VM 네트워크에 연결합니다.
- 네트워크 매핑을 구성하지 않으면 장애 조치 후에 복제본 VM이 어떤 네트워크에도 연결되지 않습니다.
- Site Recovery를 배포하는 동안 네트워크 매핑을 설정하려면 다음이 필요합니다.

	- 원본 Hyper-V 호스트 서버의 VM이 VMM VM 네트워크에 연결되어야 합니다. 해당 네트워크가 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
	- 복구에 사용할 보조 클라우드에 해당 VM 네트워크가 구성되어 있는지 확인합니다. 해당 VM 네트워크는 보조 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.

- 네트워크 매핑의 작동 원리에 대해 [알아보세요](site-recovery-network-mapping.md).

## 단일 VMM 서버를 사용한 배포 준비

단일 VMM 서버만 있으면 VMM 클라우드의 Hyper-V 호스트에 있는 VM을 [Azure](site-recovery-vmm-to-azure.md) 또는 보조 VMM 클라우드에 복제할 수 있습니다. 클라우드 간 복제는 원활하지 않으므로 첫 번째 옵션이 권장되지만 두 번째 방식을 사용해야 하는 경우에는 다음 작업을 수행해야 합니다.

1. **Hyper-V VM에 VMM을 설정합니다**. 이를 수행하는 경우 VMM에 사용되는 SQL Server 인스턴스를 동일한 VM에 배치하는 것이 좋습니다. 이렇게 하면 VM을 하나만 만들면 되므로 시간이 절약됩니다. SQL Server의 원격 인스턴스를 사용하려는 경우 중단이 발생하면 VMM을 복구하기 전에 해당 인스턴스를 복구해야 합니다.
2. **VMM 서버에 클라우드가 2개 이상 구성되도록 해야 합니다**. 하나의 클라우드는 복사할 VM을 포함하고, 다른 클라우드는 보조 위치로 사용됩니다. 보호하려는 VM이 포함된 클라우드는 [필수 구성 요소](#on-premises-prerequisites)를 충족해야 합니다.
3. 이 문서에 설명된 대로 Site Recovery를 설정합니다. 자격 증명 모음에 VMM 서버를 만든 후 등록하고 복제 정책을 설정하고 복제를 사용하도록 설정합니다. 초기 복제가 네트워크를 통해 진행되도록 지정해야 합니다.
4. 네트워크 매핑을 설정하는 경우 기본 클라우드용 VM 네트워크를 보조 클라우드용 VM 네트워크에 매핑합니다.
5. Hyper-V 관리자 콘솔에서 VMM VM을 포함하는 Hyper-V 호스트의 Hyper-V 복제본을 사용하도록 설정하고 VM의 복제를 활성화합니다. Hyper-V 복제본 설정이 사이트 복구에 의해 무시되지 않도록, 사이트 복구에 의해 보호되는 클라우드에 VMM 가상 컴퓨터를 추가하지 말아야 합니다.
6. 장애 조치(Failover)에 대한 복구 계획을 생성하려면 원본과 대상에 동일한 VMM 서버를 사용합니다.
7. 다음과 같이 장애 조치를 수행하고 복구합니다.

	- 계획된 장애 조치(failover)를 통해 Hyper-V 관리자를 사용하여 수동으로 VMM VM을 보조 사이트로 장애 조치합니다.
	- VM을 장애 조치(Failover)합니다.
	- 기본 VMM VM이 복구된 후에 Azure 포털에 로그인하고 복구 서비스 자격 증명 모음으로 이동한 후 보조 사이트에서 기본 사이트로 VM의 계획되지 않은 장애 조치(failover)를 실행합니다.
	- 계획되지 않은 장애 조치(failover)가 완료되면 기본 사이트에서 모든 리소스에 다시 액세스할 수 있습니다.


### 복구 서비스 자격 증명 모음 만들기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기** > **관리** > **복구 서비스**를 클릭합니다. 또는 **찾아보기** > **복구 서비스** 자격 증명 모음 > **추가**를 클릭합니다.

	![새 자격 증명 모음](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. **이름**에 자격 증명 모음을 식별하기 위한 이름을 지정합니다. 구독이 두 개 이상인 경우 그 중에서 하나를 선택합니다.
4. [새 리소스 그룹을 만들거나](../resource-group-portal.md#create-resource-group) 기존 리소스 그룹을 선택하고 Azure 지역을 지정합니다. 이 지역에 컴퓨터가 복제됩니다. 지원되는 지역을 확인하려면 [Azure 사이트 복구 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에서 지리적 가용성을 참조하십시오.
4. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정** > **자격 증명 모음 만들기**를 클릭합니다.

	![새 자격 증명 모음](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

**대시보드** > **모든 리소스**와 주 **복구 서비스 자격 증명 모음** 블레이드에 새 자격 증명 모음이 표시될 것입니다.




## 시작

Site Recovery는 신속한 배포를 도와주는 시작 환경을 제공합니다. 시작은 필수 조건을 확인하고 Site Recovery 배포 단계를 올바른 순서대로 안내합니다.

시작에서 복제할 컴퓨터 유형과 복제할 위치를 선택합니다. 온-프레미스 서버를 설정하고, 복제 정책을 만든 후 용량 계획을 수행합니다. 인프라를 설정한 후 VM에 대한 복제를 활성화합니다. 특정 컴퓨터에 대해 장애 조치(Failover)를 실행하거나 여러 컴퓨터를 장애 조치(Failover)하는 복구 계획을 만들 수 있습니다.

Site Recovery 배포 방법을 선택하여 시작 환경을 시작합니다. 복제 요구 사항에 따라 시작 환경의 흐름이 약간 달라질 수 있습니다.

## 1단계: 보호 목표 선택

복제할 대상과 복제할 위치를 선택합니다.

1. **복구 서비스 자격 증명 모음** 블레이드에서 자격 증명 모음을 선택하고 **설정**을 클릭합니다.
2. **설정** > **시작**에서 **사이트 복구** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.
3. **보호 목표**에서 **복구 사이트**를 선택하고 **예, Hyper-V 사용**을 선택합니다. 
4. **예**를 선택하여 VMM으로 Hyper-V 호스트를 관리할 것임을 나타내고, 보조 VMM 서버가 있는 경우 **예**를 선택합니다. 단일 VMM 서버의 클라우드 간에 복제를 배포하는 경우 **아니요**를 클릭합니다. 그런 후 **OK**를 클릭합니다. 

	![목표 선택](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## 2단계: 소스 환경 설정

VMM 서버에 Azure Site Recovery 공급자를 설치하고 자격 증명 모음에 서버를 등록합니다.

1. **2단계: 인프라 준비** > **원본**을 클릭합니다. 

	![원본 설정](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. **원본 준비**에서 **+VMM**을 클릭하여 VMM 서버를 추가합니다.

	![원본 설정](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. **서버 추가** 블레이드에서 **System Center VMM 서버**가 **서버 유형**에 표시되고 VMM 서버가 [필수 조건 및 URL 요구 사항](#on-premises-prerequisites)을 만족하는지 확인합니다.
4. Azure Site Recovery 공급자 설치 파일을 다운로드합니다.
5. 등록 키를 다운로드합니다. 설정을 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다. 

	![원본 설정](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. VMM 서버에 Azure Site Recovery 공급자를 설치합니다.

> [AZURE.NOTE] Hyper-V 호스트 서버에 명시적으로 설치할 필요는 없습니다.


### Azure Site Recovery 공급자 설치

1. 각 VMM 서버에서 공급자 설치 파일을 실행합니다. VMM이 클러스터에 배포되고 공급자를 처음 설치하는 경우 활성 노드에 설치하고 설치를 완료하여 VMM 서버를 자격 증명 모음에 등록합니다. 그런 후에 다른 노드에 공급자를 설치합니다. 모든 클러스터 노드는 동일한 버전의 공급자를 실행해야 합니다.
2. 설치 중에 몇 가지 필수 구성 요소 검사가 실행되며 VMM 서비스를 중지하기 위한 권한이 요청됩니다. 설정이 완료되면 VMM 서비스가 자동으로 다시 시작됩니다. VMM 클러스터에 설치하는 경우 클러스터 역할을 중지하라는 메시지가 표시됩니다.

2.  Microsoft 업데이트 정책에 따라 공급자 업데이트가 설치되도록 **Microsoft 업데이트**에서 업데이트를 선택할 수 있습니다.
3. **설치**에서 기본 공급자 설치 위치를 수락하거나 수정하고 **설치**를 클릭합니다.

	![설치 위치](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. 설치가 완료되면 **등록**을 클릭하여 자격 증명 모음에 서버를 등록합니다.

	![설치 위치](./media/site-recovery-vmm-to-vmm/provider-register.png)

4. VMM 서버에서 실행 중인 공급자가 인터넷을 통해 Site Recovery에 연결하는 방법을 **프록시 설정**에서 지정합니다.

	- 공급자가 직접 연결되도록 하려면 **프록시 없이 직접 연결**을 선택합니다.
	- - 현재 서버에 설정된 프록시를 사용하여 연결하려면 **기존 프록시 설정을 사용하여 연결**을 선택합니다.
	- 기존 프록시에 인증이 필요하거나 공급자 연결에 대해 사용자 지정 프록시를 사용하려면 **사용자 지정 프록시 설정을 사용하여 연결**을 선택합니다.
	- 사용자 지정 프록시를 사용하는 경우 주소, 포트 및 자격 증명을 지정해야 합니다.
	- 프록시를 사용하는 경우 [필수 조건](#provider-and-agent-prerequisites)에 설명된 URL을 이미 허용했어야 합니다.
	- 사용자 지정 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. VMM 콘솔에서 VMM 실행 계정 설정을 수정할 수 있습니다. **설정**에서 **보안** > **실행 계정**을 확장하고 DRAProxyAccount의 암호를 수정합니다. 이 설정이 적용되도록 VMM 서비스를 다시 시작해야 합니다.

	![인터넷](./media/site-recovery-vmm-to-vmm/provider3.png)

4. **자격 증명 모음 설정**에서 **찾아보기**를 클릭하고 다운로드한 키 파일을 선택합니다. Azure 구독 및 자격 증명 모음 이름을 지정합니다.

	![서버 등록](./media/site-recovery-vmm-to-vmm/provider-key2.png)

5. **등록** > **인증서 저장 위치**에서 **다음**을 클릭합니다. 암호화를 위한 인증서는 이 시나리오에서 사용되지 않습니다. 이 인증서는 Azure 저장소에 복제하는 경우에만 사용됩니다.

8. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다.
9. VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화하려면 **클라우드 메타데이터 동기화**를 활성화합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다. **다음**을 클릭하여 프로세스를 완료합니다.

	![서버 등록](./media/site-recovery-vmm-to-vmm/provider-sync.png)

10. 등록이 시작됩니다. 등록이 완료되면 자격 증명 모음의 **설정** > **서버** 블레이드에 VMM 서버가 표시됩니다.
11. Site Recovery 콘솔에서 서버를 사용할 수 있게 되면 **원본** > **원본 준비**에서 VMM 서버를 선택하고 Hyper-V 호스트가 있는 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.

#### 명령줄 설치

다음 명령줄에서 Azure Site Recovery 공급자를 설치할 수 있습니다. 이 방법은 Windows Server 2012 R2용 Server Core에 공급자를 설치하는 데 사용할 수 있습니다.

1. 공급자 설치 파일 및 등록 키를 폴더로 다운로드합니다. 예: C:\\ASR.
2. System Center Virtual Machine Manager 서비스를 중지합니다.
3. 상승된 명령 프롬프트에서 다음 명령을 실행하여 공급자 설치 관리자를 추출합니다.

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. 다음 명령을 실행하여 공급자를 설치합니다.

    	C:\ASR> setupdr.exe /i

5. 그런 후 다음 이 명령을 실행하여 서버를 자격 증명 모음에 등록합니다.

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

매개 변수는 다음에 위치합니다.

 - **/Credentials** : 등록 키 파일이 있는 위치를 지정하는 필수 매개 변수입니다.  
 - **/FriendlyName** : Azure Site Recovery 포털에 나타나는 Hyper-V 호스트 서버의 이름에 대한 필수 매개 변수입니다.
 - **/EncryptionEnabled** : VMM에서 Azure로 복제할 때만 사용되는 선택적 매개 변수입니다.
 - **/proxyAddress** : 프록시 서버의 주소를 지정하는 선택적 매개 변수입니다.
 - **/proxyport** : 프록시 서버의 포트를 지정하는 선택적 매개 변수입니다.
 - **/proxyUsername** : (프록시가 인증을 필요로 하는 경우) 프록시 사용자 이름을 지정하는 선택적 매개 변수입니다.
 - **/proxyPassword** : (프록시가 인증을 필요로 하는 경우) 프록시 서버를 인증하기 위한 암호를 지정하는 선택적 매개 변수입니다.  

## 3단계: 대상 환경 설정

대상 VMM 서버 및 클라우드를 선택합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 대상 VMM 서버를 선택합니다. 
2.	Site Recovery와 동기화된 서버의 클라우드가 표시됩니다. 대상 클라우드를 선택합니다.

	![대상](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## 4단계: 복제 설정 지정

1. 새 복제 정책을 만들려면 **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 클릭합니다.

	![네트워크](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다. 원본 및 대상 유형은 **Hyper-V**여야 합니다.
3. **Hyper-V 호스트 버전**에서 호스트에서 실행되는 운영 체제를 선택합니다.

	> [AZURE.NOTE] VMM 클라우드는 다른(지원) 버전의 Windows Server를 실행하는 Hyper-V 호스트를 포함할 수 있지만 복제 정책은 동일한 운영 체제를 실행하는 호스트에 적용됩니다. 호스트에서 두 개 이상의 운영 체제 버전이 실행되는 경우 별도의 복제 정책을 만듭니다.

4. **인증 유형** 및 **인증 포트**에서 기본 및 복구 Hyper-V 호스트 서버 간에 트래픽을 인증하는 방법을 지정합니다. 작동하는 Kerberos 환경이 구성되어 있지 않으면 **인증서**를 선택합니다. Azure Site Recovery가 HTTPS 인증을 위한 인증서를 자동으로 구성합니다. 수동으로 수행할 작업은 없습니다. 기본적으로 Hyper-V 호스트 서버의 Windows 방화벽에서 포트 8083과 8084(인증서용)가 열립니다. **Kerberos**를 선택하면 호스트 서버의 상호 인증에 Kerberos 티켓이 사용됩니다. 이 설정은 Windows Server 2012 R2에서 실행 중인 Hyper-V 호스트 서버와만 관련이 있습니다.
3. **복사 빈도**에서 초기 복제 후 델타 데이터를 복제할 빈도(30초 마다, 5분마다 또는 15분마다)를 지정합니다.
4. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다.
6. **응용 프로그램 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(1-12시간)를 지정합니다. Hyper-V는 두 가지 유형의 스냅숏, 즉 전체 가상 컴퓨터의 증분 스냅숏을 제공하는 표준 스냅숏과 가상 컴퓨터 내 응용 프로그램 데이터의 지정 시간 스냅숏을 만드는 응용 프로그램 일치 스냅숏을 사용합니다. 응용 프로그램 일치 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏을 만들 때 응용 프로그램이 일관된 상태가 되도록 합니다. 응용 프로그램 일치 스냅숏을 사용하도록 설정하면 원본 가상 컴퓨터에서 실행되는 응용 프로그램의 성능에 영향을 줍니다. 구성하는 추가 복구 지점 수보다 작은 값을 설정해야 합니다.
7. **데이터 전송 압축**에서 전송되는 복제된 데이터를 압축할지 여부를 지정합니다.
8. **복제본 VM 삭제**를 선택하여 원본 VM에 대해 보호를 사용하지 않도록 설정하는 경우 복제본 가상 컴퓨터가 삭제되도록 지정합니다. 이 설정을 사용하도록 지정하는 경우 보호가 설정되지 않은 원본 VM이 Site Recovery 콘솔에서 제거되고, VMM에 대한 Site Recovery 설정이 VMM 콘솔에서 제거되고, 복제본이 삭제됩니다.
3. 네트워크를 통해 복제하는 경우 **초기 복제 방법**에서 초기 복제를 시작할지 또는 예약할지를 지정합니다. 네트워크 대역폭을 절약하려면 사용량이 많지 않은 시간에 예약하는 것이 좋습니다. 그런 후 **OK**를 클릭합니다.

	![복제 정책](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. 새 정책을 만들면 새 정책이 자동으로 VMM 클라우드에 연결됩니다. **복제 정책**에서 **확인**을 클릭합니다. **설정** > **복제** > 정책 이름 > **VMM 클라우드 연결**에서 추가 VMM 클라우드(및 그 안에 포함된 VM)를 이 복제 정책과 연결할 수 있습니다.

	![복제 정책](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### 오프라인 초기 복제 준비

초기 데이터 복사본에 대해 오프라인 복제를 수행할 수 있습니다. 이 작업은 다음과 같이 준비할 수 있습니다.

- 원본 서버에서 데이터 내보내기가 수행되는 경로 위치를 지정합니다. NTFS에 대해 모든 권한을 할당하고 내보내기 경로의 VMM 서비스에 공유 권한을 할당합니다. 대상 서버에서 데이터 가져오기가 수행되는 경로 위치를 지정합니다. 이 가져오기 경로에 동일한 사용 권한을 할당합니다.
- 가져오기 또는 내보내기 경로가 공유되는 경우 공유가 있는 원격 컴퓨터의 VMM 서비스 계정에 대해 Administrator, Power User, Print Operator 또는 Server Operator 그룹 구성원 자격을 할당합니다.
- 실행 계정을 사용하여 호스트를 추가하는 경우 가져오기 및 내보내기 경로에서 VMM의 실행 계정에 읽기 및 쓰기 권한을 할당합니다.
- Hyper-V는 루프백 구성을 지원하지 않으므로 Hyper-V 호스트 서버로 사용되는 컴퓨터에는 가져오기 및 내보내기 공유가 있으면 안 됩니다.
- 보호할 가상 컴퓨터를 포함하는 Active Directory의 각 Hyper-V 호스트 서버에서 다음과 같이 제한 위임을 사용하도록 설정하고 가져오기 및 내보내기 경로가 있는 원격 컴퓨터를 신뢰하도록 구성합니다.
	1. 도메인 컨트롤러에서 **Active Directory 사용자 및 컴퓨터**를 엽니다.
	2. 콘솔 트리에서 **DomainName** > **컴퓨터**를 클릭합니다.
	3. Hyper-V 호스트 서버 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
	4. **위임** 탭에서 **지정한 서비스에 대한 위임용으로만 이 컴퓨터 트러스트**를 클릭합니다.
	5. **모든 인증 프로토콜 사용**을 클릭합니다.
	6. **추가** > **사용자 및 컴퓨터**를 클릭합니다.
	7. 내보내기 경로를 호스트하는 컴퓨터의 이름을 입력하고 **확인**을 클릭합니다. 사용 가능한 서비스 목록에서 Ctrl 키를 누른 채 **cifs** > **확인**을 클릭합니다. 가져오기 경로를 호스트하는 컴퓨터 이름에 대해 반복합니다. 필요에 따라 추가 Hyper-V 호스트 서버에 대해 반복합니다.


### 네트워크 매핑 구성

원본 및 대상 네트워크 간에 네트워크 매핑을 설정합니다.

- 네트워크 매핑의 역할에 대한 빠른 개요를 [읽어보세요](#prepare-for-network-mapping). 좀 더 자세한 설명을 [읽어보세요](site-recovery-network-mapping.md).
- VMM 서버의 가상 컴퓨터가 VM 네트워크에 연결되어 있는지 확인합니다.

다음과 같이 매핑을 구성합니다.

1. **설정** > **Site Recovery 인프라** > **네트워크 매핑** > **네트워크 매핑**에서 **+네트워크 매핑**을 클릭합니다.

	![네트워크 매핑](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. **네트워크 매핑 추가** 탭에서 원본 및 대상 VMM 서버를 선택합니다. VMM 서버와 연결된 VM 네트워크가 검색됩니다.
3. **원본 네트워크**에서 기본 VMM 서버에 연결된 VM 네트워크 목록에서 사용하려는 네트워크를 선택합니다. 
6. **대상 네트워크**에서 보조 VMM 서버에서 사용하려는 네트워크를 선택합니다. 그런 후 **OK**를 클릭합니다.

	![네트워크 매핑](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

네트워크 매핑이 시작되면 다음과 같은 동작이 발생합니다.

- 원본 VM 네트워크에 해당하는 기존 복제본 가상 컴퓨터는 모두 대상 VM 네트워크에 연결됩니다.
- 원본 VM 네트워크에 연결된 새 가상 컴퓨터는 복제 후 매핑된 대상 네트워크에 연결됩니다.
- 새 네트워크로 기존 매핑을 수정하면 복제본 가상 컴퓨터가 새 설정을 사용하여 연결됩니다.
- 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제본 가상 컴퓨터가 장애 조치(Failover) 후에 대상 서브넷에 연결됩니다. 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.

## 5단계: 용량 계획

기본 인프라를 설치했으니 용량 계획에 대해 생각해 보고 추가 리소스가 필요한지 파악할 수 있습니다.

Site Recovery는 원본 환경, 사이트 복구 구성 요소, 네트워킹 및 저장소에 적절한 리소스를 할당할 수 있도록 도와주는 Excel 기반 Capacity Planner를 제공합니다. 평균 VM, 디스크 및 저장소 수를 기반으로 예측하는 빠른 모드 또는 워크로드 수준에서 숫자를 입력하는 세부 모드에서 플래너를 실행할 수 있습니다. 시작하기 전에 다음을 수행해야 합니다.

- VM, VM당 디스크, 디스크당 저장소를 포함하여 복제 환경에 대한 정보를 수집합니다.
- 복제된 델타 데이터에 대한 일일 변경(이탈)률을 예측합니다. [Hyper-V 복제본용 Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057)를 사용하면 이 작업을 간편하게 수행할 수 있습니다.

1.	**다운로드**를 클릭하여 도구를 다운로드한 후 실행하세요. 도구와 함께 제공되는 [문서를 읽어보세요](site-recovery-capacity-planner.md).
2.	작업을 마쳤으면 **Capacity Planner를 실행하셨습니까**?에서 **예**를 선택합니다.

	![용량 계획](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### 대역폭 제어

Capacity Planner Hyper-V 복제본을 사용하여 실시간 델타 복제 정보를 수집했으면 Excel 기반의 Capacity Planner 도구로 복제(초기 및 델타) 복제에 필요한 대역폭을 계산할 수 있습니다. 복제에 사용되는 대역폭 크기를 제어하려면 그룹 정책 또는 Windows PowerShell을 사용하여 NetQos 정책을 구성할 수 있습니다. 사용할 수 있는 몇 가지 방법은 다음과 같습니다.

**PowerShell** | **세부 정보**
--- | ---
**New -NetQosPolicy "대상 서브넷에 대한 QoS"** | Windows Server 2012 R2를 실행하는 Hyper-V 호스트에서 보조 서브넷으로의 트래픽을 제한합니다. 기본 및 보조 서브넷이 다른 경우에 사용합니다.
**New -NetQosPolicy "대상 포트에 대한 QoS"** | Windows Server 2012 R2를 실행하는 Hyper-V 호스트에서 대상 포트로의 트래픽을 제한합니다.
**New -NetQosPolicy "VMM의 트래픽 제한"** | vmms.exe의 트래픽을 제한합니다. Hyper-V 트래픽과 실시간 마이그레이션 트래픽이 제한됩니다. IP 프로토콜 및 포트를 일치시켜 구체화할 수 있습니다.

대역폭 가중치 설정을 사용하거나 보조당 비트 수로 트래픽을 제한할 수 있습니다. 클러스터를 사용하는 경우 모든 클러스터 노드에 대해 이 작업을 수행해야 합니다. 자세한 내용은 다음을 참조하세요.


- [Throttling Hyper-V Replica Traffic(Hyper-V 복제본 트래픽 제한)](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)에 대한 Thomas Maurer의 블로그
- [New-NetQosPolicy cmdlet](https://technet.microsoft.com/library/hh967468.aspx)에 대한 추가 정보


## 6단계: 복제 활성화 

이제 다음과 같이 복제를 활성화합니다.

1. **2단계: 응용 프로그램 복제** > **원본**을 클릭합니다. 처음으로 복제를 활성화한 후 자격 증명 모음에서 **+복제**를 클릭하여 추가 컴퓨터에 대해 복제를 활성화합니다.

	![복제 활성화](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. **원본** 블레이드에서 복제하려는 Hyper-V 호스트가 배치되는 VMM 서버 및 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.

	![복제 활성화](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. **대상** 블레이드에서 보조 VMM 서버 및 클라우드를 확인합니다.
4. **가상 컴퓨터**의 목록에서 보호하려는 VM을 선택합니다. 

	![가상 컴퓨터 보호 사용](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

설정 > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 가상 컴퓨터가 장애 조치(Failover)를 수행할 준비가 되어 있습니다.


>[AZURE.NOTE] 또한 VMM 콘솔에서 가상 컴퓨터에 대해 보호를 사용하도록 설정할 수 있습니다. 가상 컴퓨터 속성의 **Azure Site Recovery** 탭에 있는 도구 모음에서 **보호 사용**을 클릭합니다.

복제를 사용하도록 설정하면 **설정** > **복제된 항목** > vm-name에서 VM에 대한 속성을 볼 수 있습니다 **필수** 대시보드에서 VM 및 해당 상태에 대한 복제 정책 정보를 볼 수 있습니다. 자세한 내용을 보려면 **속성**을 클릭합니다.

### 기존 가상 컴퓨터 등록

Hyper-V 복제본을 사용하여 복제 중인 기존 가상 컴퓨터가 VMM에 있는 경우 Azure Site Recovery 보호를 위해 다음과 같이 등록할 수 있습니다.

1. 기존 VM을 호스트하는 Hyper-V 서버가 기본 클라우드에 있고 복제본 가상 컴퓨터를 호스트하는 Hyper-V 서버가 보조 클라우드에 있는지 확인합니다.
2. 복제 정책이 기본 VMM 클라우드에 대해 구성되어 있는지 확인합니다.
2. 기본 가상 컴퓨터에 대해 보호를 사용하도록 설정합니다. Azure Site Recovery 및 VMM에서 동일한 복제본 호스트와 가상 컴퓨터가 검색되는지 확인하고, Azure Site Recovery에서 지정된 설정을 사용하여 복제를 다시 사용하고 설정합니다.


## 7단계: 배포 테스트

배포를 테스트하기 위해 단일 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행하거나 하나 이상의 가상 컴퓨터를 포함한 복구 계획을 만들 수 있습니다.

### 장애 조치(Failover) 준비

- 배포를 완전하게 테스트하려면 복제된 컴퓨터가 예상대로 작동할 수 있는 인프라가 필요합니다. Active Directory 및 DNS를 테스트하려면 DNS를 사용하여 도메인 컨트롤러로 가상 컴퓨터를 만들고 Azure Site Recovery를 사용하여 이 가상 컴퓨터를 Azure에 복제합니다. [Active Directory에 대한 테스트 장애 조치(failover) 고려 사항](site-recovery-active-directory.md#considerations-for-test-failover)에 대해 자세히 알아보세요.
- 이 문서에서는 네트워크를 사용하지 않고 테스트 장애 조치(failover)를 실행하는 방법을 설명합니다. 이 옵션은 VM 장애 조치가 수행되는지 테스트하지만 VM에 대한 네트워크 설정은 테스트하지 않습니다. 다른 옵션에 대해서도 [자세히 알아보세요](site-recovery-failover.md#run-a-test-failover).
- 테스트 장애 조치(Failover) 대신 계획되지 않은 장애 조치(Failover)를 실행하려면 다음 사항에 주의하세요.

	- 가능한 경우 계획되지 않은 장애 조치를 실행하기 전에 주 컴퓨터를 종료해야 합니다. 이렇게 하면 원본 및 복제본 컴퓨터가 동시에 실행되지 않아도 됩니다. 
	- 계획되지 않은 장애 조치를 실행하면 주 컴퓨터에서 데이터 복제를 중지하므로 계획되지 않은 장애 조치가 시작된 후 모든 데이터 델타가 전송되지 않습니다. 또한 복구 계획에서 계획되지 않은 장애 조치(Failover)를 실행하면 오류가 발생하더라도 완료될 때까지 실행됩니다.




### 테스트 장애 조치(Failover) 실행

1. 단일 VM을 장애 조치(Failover)하려면 **설정** > **복제된 항목**에서 VM > **+테스트 장애 조치(Failover)**를 클릭합니다.

	![테스트 장애 조치(Failover)](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. 복구 계획을 장애 조치(Failover)하려면 **설정** > **복구 계획**에서 계획을 마우스 오른쪽 단추로 클릭하고 **테스트 장애 조치(Failover)**를 클릭합니다. 복구 계획을 만들려면 [다음 지침을 따릅니다](site-recovery-create-recovery-plans.md).
2. **테스트 장애 조치(Failover)**에서 **없음**을 선택합니다. 이 옵션을 사용하여 VM 장애 조치가 예상대로 수행되는지 테스트할 수 있지만 복제된 VM은 어떤 네트워크에도 연결되지 않습니다.

	![테스트 장애 조치(Failover)](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. **확인**을 클릭하여 장애 조치(Failover)를 시작합니다. VM을 클릭하고 속성을 열거나 **설정** > **작업** > **Site Recovery 작업**의 **테스트 장애 조치(Failover)**에서 진행률을 추적할 수 있습니다.
4. 장애 조치(Failover) 작업이 **테스트 완료** 단계에 도달하면 다음 작업을 수행합니다.

	-  보조 VMM 클라우드에서 복제본 VM을 확인합니다.
	-  **테스트 완료**를 클릭하여 테스트 장애 조치(Failover)를 마칩니다.
	-  **참고**를 클릭하여 테스트 장애 조치(Failover)와 연관된 항목을 기록한 후 저장합니다.

5. 테스트 가상 컴퓨터는 복제본 가상 컴퓨터가 있는 호스트와 동일한 호스트에 생성됩니다. 복제본 가상 컴퓨터가 있는 동일한 클라우드에 추가됩니다.
6. VM이 성공적으로 시작되는지 확인한 후 **테스트 장애 조치(Failover)가 완료되었습니다.**를 클릭합니다. 이 단계에서는 테스트 장애 조치(Failover) 중에 자동으로 생성된 모든 요소가 삭제됩니다.  

	> [AZURE.NOTE] 테스트 장애 조치(Failover)가 2주 이상 지속되면 강제로 완료됩니다.



### DNS를 복제본 VM IP 주소로 업데이트

장애 조치(Failover) 후에 복제본 VM이 기본 가상 컴퓨터와 같은 IP 주소를 갖지 않을 수 있습니다.

- 가상 컴퓨터가 시작되면 사용 중인 DNS 서버가 업데이트됩니다.
- 다음에 따라 DNS를 수동으로 업데이트할 수도 있습니다.

#### IP 주소 검색

다음 샘플 스크립트를 실행하여 IP 주소를 검색합니다.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

#### DNS 업데이트

다음 샘플 스크립트에서 이전 샘플 스크립트를 사용하여 검색한 IP 주소를 지정하고 실행하여 DNS를 업데이트합니다.

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## 다음 단계

배포가 설정되고 실행된 후에는 다양한 장애 조치(Failover)에 대해 [자세히 알아보세요](site-recovery-failover.md).

<!---HONumber=AcomDC_0601_2016-->