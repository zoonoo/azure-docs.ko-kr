
<properties
	pageTitle="단일 VMM 서버로 보호 설정"
	description="Azure Site Recovery는 온-프레미스 VMM 클라우드에 있는 가상 컴퓨터의 복제, 장애 조치 및 복구를 Azure 또는 보조 VMM 클라우드로 조정합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="10/07/2015"
	ms.author="raynew"/>

#  단일 VMM 서버로 보호 설정

## 개요

Azure Site Recovery는 여러 배포 시나리오에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 배포 시나리오의 전체 목록은 [Azure Site Recovery 개요](site-recovery-overview.md)를 참조하세요.

인프라에 단일 VMM 서버가 있는 경우, VMM 클라우드의 가상 컴퓨터를 Azure에 복제하기 위해 사이트 복구를 배포하거나 단일 VMM 서버의 클라우드 간에 복제할 수 있습니다. 이 배포에서는 장애 조치 및 복구가 원활하지 않으므로 두 VMM 서버(각 사이트에 하나씩)에 배포할 수 없는 경우에만 이를 수행하는 것이 좋습니다. 복구의 경우, Azure Site Recovery 콘솔 외부에서 VMM 서버를 통해 수동으로 장애를 복구해야 합니다(Hyper-V 관리자 콘솔에서 Hyper-V 복제본 사용).

다음과 같은 몇 가지 방법으로 단일 VMM 서버를 사용하여 복제를 설정할 수 있습니다.

### 독립 실행형 배포

독립 실행형 VMM 서버를 기본 사이트에서 가상 컴퓨터로 배포하고 이 가상 컴퓨터를 사이트 복구 및 Hyper-V 복제본을 사용하여 보조 사이트로 복제합니다. 가동 중지 시간을 줄이기 위해 SQL Server를 VMM 가상 컴퓨터에 설치할 수 있습니다. VMM에서 원격 SQL Server를 사용하는 경우 VMM 서버를 복구하기 전에 먼저 해당 서버를 복구해야 합니다.

![독립 실행형 가상 VMM 서버](./media/site-recovery-single-vmm/SingleVMMStandalone.png)

### 클러스터 배포

VMM을 항상 사용할 수 있도록 하려면 이를 indows 장애 조치 클러스터에서 가상 컴퓨터로 배포할 수 있습니다. 워크로드 가용성을 보장하고 VMM을 실행 중인 호스트의 하드웨어 장애 조치로부터 복구하기 때문에 중요한 워크로드를 VMM을 통해 관리하는 경우에 유용합니다. 사이트 복구를 통해 단일 VMM 서버를 배포하려면 지리적으로 분리된 사이트에서 확대된 클러스터를 통해 VMM 가상 컴퓨터를 배포해야 합니다. VMM에서 사용하는 SQL Server 데이터베이스는 보조 사이트에서 복제를 통해 SQL Server AlwaysOn 가용성 그룹과 함께 보호되어야 합니다. 재해가 발생하면 VMM 서버 및 해당 SQL Server 데이터베이스가 장애 조치하고 보조 사이트에서 액세스됩니다.

![클러스터된 가상 VMM 서버](./media/site-recovery-single-vmm/SingleVMMCluster.png)


## 시작하기 전에

- 이 연습 단계는 하나의 독립 실행형 VMM 서버로 사이트 복구를 배포하는 방법에 대해 설명합니다.
- 배포를 시작하기 전에 [필수 조건](site-recovery-vmm-to-vmm.md/#before-you-start)이 있는지 확인합니다.
- 단일 VMM 서버에는 구성된 둘 이상의 클라우드가 있어야 합니다. 하나의 클라우드는 보호되는 클라우드 역할을 하며 다른 클라우드는 보호를 수행합니다.
- 보호할 클라우드에는 다음이 포함되어야 합니다.
	- 하나 이상의 VMM 호스트 그룹
	- 각 호스트 그룹에 있는 하나 이상의 Hyper-V 호스트 서버
	- 각 호스트 서버에 있는 하나 이상의 Hyper-V 가상 컴퓨터

이 시나리오를 설정하는 동안 문제가 발생할 경우 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 문의 사항을 게시하세요.



## 단일 서버 배포를 구성합니다.

1. VMM가 배포되지 않는 경우 SQL Server 데이터베이스가 설치된 가상 컴퓨터에서 VMM을 설정합니다. [시스템 요구 사항](https://technet.microsoft.com/library/dn771747.aspx) 읽기 
2. VMM 서버에 둘 이상의 클라우드를 설정합니다. 자세한 정보:

	- [System Center 2012 R2 VMM을 사용하는 사설 클라우드의 새로운 기능](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=) 및 [VMM 2012와 클라우드의 새로운 기능](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html). 
	- [VMM 클라우드 패브릭 구성](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- [VMM에서 사설 클라우드 만들기](https://technet.microsoft.com/library/jj860425.aspx) 및 [연습: System Center 2012 SP1 VMM을 사용하는 사설 클라우드 만들기](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
3. 보호할 가상 컴퓨터에 원본 Hyper-V 호스트 서버를 추가하면 보호하고 있는 클라우드(원본 클라우드)에 배치됩니다. 보호를 제공할 VMM 서버의 클라우드에 대상 Hyper-V 호스트 서버를 추가합니다.
4. Azure Site Recovery 자격 증명 모음을 [만들고](site-recovery-vmm-to-vmm.md/#step-1-create-a-site-recovery-vault) 자격 증명 모음 등록 키를 생성합니다.
4. VMM 서버에 Azure Site Recovery 공급자를 [설치](site-recovery-vmm-to-vmm.md/#step-3-install-the-azure-site-recovery-provider)하고 자격 증명 모음에 서버를 등록합니다. 
5. 사이트 복구 포털에 클라우드가 나타나는지 확인하고 [클라우드 보호 설정을 구성](site-recovery-vmm-to-vmm.md/#step-4-configure-cloud-protection-settings)합니다.
	- **원본 위치** 및 **대상 위치**에서 단일 VMM 서버의 이름을 지정합니다.
	- **복제 방법**에서 클라우드가 동일한 서버에 위치하기 때문에 초기 복제에 대한 **네트워크를 통해**를 선택합니다.

6. [네트워크 매핑 구성](site-recovery-vmm-to-vmm.md/#step-5-configure-network-mapping)(선택 사항):

	- **원본** 및 **대상**에서 단일 VMM 서버의 이름을 지정합니다.
	- **원본의 네트워크**에서 보호하고 있는 클라우드에 대해 구성된 VM 네트워크를 선택합니다.
	- **대상의 네트워크**에서 보호하려는 클라우드에 대해 구성된 VM 네트워크를 선택합니다.
	- 네트워크 매핑은 동일한 VMM 서버에서 두 VM(가상 컴퓨터) 네트워크 간에 구성할 수 있습니다. 동일한 VMM 네트워크가 두 개의 다른 사이트에 있는 경우 동일한 네트워크 간에 매핑할 수 있습니다.
7. 보호하려는 VMM 클라우드에서 가상 컴퓨터에 대한 [보호를 사용하도록 설정](site-recovery-vmm-to-vmm.md/#step-7-enable-virtual-machine-protection)할 수 있습니다. 
7. Hyper-V 관리자 콘솔에서 Hyper-V 복제본을 사용하여 VMM 가상 컴퓨터에 대한 복제를 설정합니다. VMM 가상 컴퓨터는 일부 VMM 클라우드에 추가할 수 없습니다.


## 장애 조치 및 복구

### 복구 계획 만들기

복구 계획은 함께 장애 조치 및 복구되어야 하는 가상 컴퓨터를 그룹화합니다.

1. **원본** 및 **대상**에서 복구 계획을 만들 때 단일 VMM 서버의 이름을 지정합니다. **가상 컴퓨터 선택**에서 기본 클라우드와 연결된 가상 컴퓨터가 표시됩니다.
2. 그런 다음 [복구 계획을 만들고 사용자 지정합니다](https://msdn.microsoft.com/library/azure/dn337331.aspx).


### 복구

재해가 발생할 경우 다음 단계를 사용하여 작업을 복구할 수 있습니다.

1. Hyper-V 관리자 콘솔에서 복구 사이트로 복제본 VMM 가상 컴퓨터를 수동으로 장애 복구합니다.
2. VMM 가상 컴퓨터가 복구된 후 포털에서 Hyper-V 복구 관리자 콘솔에 로그인하여 기본 사이트에서 복구 사이트로 가상 컴퓨터의 계획되지 않은 장애 조치를 수행할 수 있습니다.
3.  계획되지 않은 장애 조치가 완료된 후 사용자가 기본 사이트에서 모든 리소스에 액세스할 수 있습니다.


 

<!---HONumber=Oct15_HO2-->