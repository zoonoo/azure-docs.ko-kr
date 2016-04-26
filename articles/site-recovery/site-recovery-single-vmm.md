
<properties
	pageTitle="Azure Site Recovery: 단일 VMM 서버에 Hyper-V 가상 컴퓨터 복제 | Microsoft Azure"
	description="이 문서에서는 단일 VMM 서버만 있는 경우 Hyper-V 가상 컴퓨터를 복제하는 방법을 설명합니다."
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
	ms.date="03/30/2016"
	ms.author="raynew"/>

#  단일 VMM 서버에서 Hyper-V 가상 컴퓨터 복제

이 문서에서는 배포에 VMM 서버가 하나만 있는 경우 VMM 클라우드에서 Hyper-V 호스트 서버에 있는 Hyper-V 가상 컴퓨터를 복제하는 방법을 설명합니다.

이 문서를 읽은 후 질문이 있다면 아래쪽 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시해 주세요.

## 개요

여러 가지 방법으로 VMM 클라우드에서 Hyper-V 호스트에 있는 Hyper-V VM을 복제할 수 있습니다.

- Azure에 복제 
- 보조 VMM 사이트에 복제

그러나 보조 VMM 위치로 복제하고 싶지만 배포에 VMM 서버가 하나뿐인 경우에는 어떻게 될까요?

이 시나리오에는 두 가지 옵션이 있습니다.

- VMM 클라우드의 Hyper-V VM을 Azure로 복제. 이 시나리오에 대해 [자세히 알아봅니다](site-recovery-vmm-to-azure.md).
- 단일 VMM 서버의 클라우드 간 복제

두 번째 옵션의 경우 장애 조치(failover) 및 복구가 원활하지 못하고 다수의 수동 단계가 필요하기 때문에 첫 번째 옵션을 선택하는 것이 좋습니다. Azure 대신 사이트 간에 복제하려는 경우 두 가지 옵션이 있습니다.


## 단일 독립 실행형 VMM 서버로 사이트 간 복제

이 시나리오에서 사이트 간에 복제하려면 단일 VMM 서버를 기본 사이트에서 가상 컴퓨터로 배포하고 Site Recovery 및 Hyper-V 복제본을 사용하여 이 VM을 보조 사이트로 복제합니다. 다음을 수행합니다.

1. Hyper-V VM에 VMM을 설정합니다. 이를 수행하는 경우 VMM에 사용되는 SQL Server 인스턴스를 동일한 VM에 배치하는 것이 좋습니다. 이렇게 하면 VM을 하나만 인스턴스화하기 때문에 시간이 절약됩니다. SQL Server의 원격 인스턴스를 사용하려는 경우 중단이 발생하면 VMM을 복구하기 전에 해당 인스턴스를 복구해야 합니다.
2. VMM 서버에 클라우드가 2개 이상 구성되도록 해야 합니다. 하나의 클라우드는 복사할 VM을 포함하고, 다른 클라우드는 보조 위치로 사용됩니다. 보호하려는 VM이 포함된 클라우드에는 다음이 있어야 합니다.

	- 각 호스트 그룹에 하나 이상의 Hyper-V 호스트 서버가 포함된 하나 이상의 VMM 호스트 그룹
	- 각 Hyper-V 호스트 서버에 있는 하나 이상의 Hyper-V 가상 컴퓨터
3. 사이트 복구 자격 증명 모음을 만들고, 자격 증명 모음 등록 키를 생성하여 다운로드 하고, 자격 증명 모음에 포함된 VMM 서버를 등록합니다. 등록 중에 VMM 서버에 Azure Site Recovery 공급자를 설치합니다.
4. VMM VM에 하나 이상의 클라우드를 설정하고, 이들 클라우드를 보호할 VM을 포함하는 Hyper-V 호스트를 추가합니다.
3. Azure Site Recovery에서 클라우드에 대한 보호 설정을 구성합니다. 원본 및 대상 위치에서 단일 VMM 서버의 이름을 지정합니다. 네트워크 매핑을 구성하는 경우, 보호할 VM을 포함하는 클라우드에 대한 VM 네트워크를 복제할 클라우드에 대한 VM 네트워크와 매핑합니다.
4. 두 개의 클라우드가 모두 동일한 서버에 위치하기 때문에, 복제 방법으로 **네트워크를 통해**를 사용하여 보호하려는 VM에 대한 복제를 활성화합니다.
4. Hyper-V 관리자 콘솔에서 VMM VM을 포함하는 Hyper-V 호스트의 Hyper-V 복제본을 사용하도록 설정하고 VM의 복제를 활성화합니다. Hyper-V 복제본 설정이 사이트 복구에 의해 무시되지 않도록, 사이트 복구에 의해 보호되는 클라우드에 VMM 가상 컴퓨터를 추가하지 말아야 합니다.
5. 복구 계획을 생성하려면 원본과 대상에 동일한 VMM 서버를 지정합니다.

자격 증명 모음 만들기, 키 가져오기, 서버 등록 및 보호 설정은 [이 문서](site-recovery-vmm-to-vmm.md)의 지침을 따르세요.

### 중단 후

중단이 발생하면 Hyper-V VM에서 다음과 같이 워크로드를 복구합니다.

1. 계획된 장애 조치(failover)를 통해 Hyper-V 관리자를 사용하여 수동으로 VMM VM을 보조 사이트로 장애 조치합니다. 
2. VMM VM이 복구된 후에, 보조 사이트에서 Hyper-V 복구 관리자에 로그인하여 보조 사이트에서 기본 사이트로 VM의 계획되지 않은 장애 조치(failover)를 실행합니다. 워크로드 VM을 장애 조치(failover)하려면 먼저 VMM VM을 수동으로 보조 사이트에 장애 조치(failover)해야 합니다.
3. 계획되지 않은 장애 조치(failover)가 완료되면 기본 사이트에서 모든 리소스에 다시 액세스할 수 있습니다.


![독립 실행형 가상 VMM 서버](./media/site-recovery-single-vmm/single-vmm-standalone.png)

## 늘어난(stretched) 클러스터의 단일 VMM 서버로 사이트 간 복제

독립 실행형 VMM 서버를 보조 사이트로 복제되는 가상 컴퓨터로 배포하는 대신 VMM을 Windows 장애 조치(failover) 클러스터의 VM으로 배포하면 항상 사용할 수 있도록 만들 수 있습니다. 그러면 워크로드 복원력이 제공되고 하드웨어 장애로부터 보호됩니다. Site Recovery와 함께 배포하기 위해서 VMM VM은 지리적으로 떨어져 있는 사이트에 걸친 확대 클러스터에 배포되어야 합니다. 다음을 수행합니다.

1. Windows 장애 조치(failover) 클러스터의 가상 컴퓨터에 VMM을 설치하고 설치하는 동안 항상 사용이 가능하게 서버를 실행하도록 옵션을 선택합니다.
2. 보조 사이트에 데이터베이스 복제본이 존재하도록 VMM에 사용되는 SQL Server 인스턴스는 SQL Server AlwaysOn 가용성 그룹과 함께 복제되어야 합니다.

Site Recovery를 설정할 때 Site Recovery 자격 증명 모음에서 클러스터의 각 VMM 서버를 등록해야 합니다. 이렇게 하려면 활성 노드에 공급자를 설치하고 자격 증명 모음에서 해당 VMM 서버를 등록하는 것으로 설치를 마칩니다. 그런 다음 다른 노드에 공급자를 설치합니다.
 
### 중단 후 

중단이 발생하면 VMM 서버 및 해당 SQL Server 데이터베이스에 장애 조치가 수행되고 보조 사이트에서 액세스됩니다.

![클러스터된 가상 VMM 서버](./media/site-recovery-single-vmm/single-vmm-cluster.png)

## 다음 단계

VMM 간 복제에 대한 [자세한](site-recovery-vmm-to-vmm.md) Site Recovery 배포를 알아봅니다.




 

<!---HONumber=AcomDC_0413_2016-->