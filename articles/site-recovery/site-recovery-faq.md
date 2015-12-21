<properties 
	pageTitle="Site Recovery: FAQ(질문과 대답) | Microsoft Azure" 
	description="이 문서에서는 Azure Site Recovery에 대한 일반적인 질문에 대답합니다." 
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="get-started-article"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="12/07/2015" 
	ms.author="raynew"/>


# Azure Site Recovery: FAQ(질문과 대답)
## 이 문서의 내용

이 문서는 Site Recovery에 대한 질문과 대답을 제공합니다. FAQ를 읽은 후 질문이 있다면 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 게시해 주십시오.


## 지원

###Site Recovery의 기능은 무엇입니까?

Site Recovery는 온-프레미스 가상 컴퓨터 및 물리적 서버에서 Azure 또는 보조 데이터 센터로의 복제 작업을 오케스트레이션 및 자동화하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. [자세히 알아봅니다](site-recovery-overview.md).


### Site Recovery로 무엇을 보호할 수 있습니까?

- **VMware 가상 컴퓨터**: Site Recovery는 Hyper-V VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다. 
- **물리적 서버**: Site Recovery는 Windows 또는 Linux를 실행하는 물리적 서버를 보호할 수 있습니다.
- **VMware 가상 컴퓨터**: Site Recovery는 VMware VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다.


###어떤 Hyper-V VM을 보호할 수 있습니까?

배포 시나리오에 따라 다릅니다.

다음 문서에서 Hyper-V 호스트 서버 필수 조건을 확인하세요.

- [Azure로 Hyper-V VM 복제(VMM 없이)](site-recovery-hyper-v-site-to-azure.md/#before-you-start)
- [Azure로 Hyper-V VM 복제(VMM 사용)](site-recovery-vmm-to-azure.md/#before-you-start)
- [보조 데이터 센터에 Hyper-V VM 복제](site-recovery-vmm-to-vmm.md/#before-you-start)

게스트 운영 체제 관련:

- 보조 데이터 센터에 복제하는 경우에는 [Hyper-V VM에서 지원되는 게스트 운영 체제](https://technet.microsoft.com/library/mt126277.aspx)에서 Hyper-V 호스트 서버에서 실행 중인 VM에 지원되는 게스트 운영 체제 목록을 참조하세요.
- Azure에 복제하는 경우에는 [Azure에서 지원되는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) 모든 게스트 운영 체제를 Site Recovery에서도 지원합니다.

Site Recovery는 지원되는 VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다.

### Hyper-V가 클라이언트 운영 체제에서 실행 중인 경우 VM을 보호할 수 있습니까?

아니요, 이 기능은 지원되지 않습니다. 그 대신 Azure 또는 보조 데이터 센터에 [물리적 클라이언트 컴퓨터를 복제](site-recovery-vmware-to-azure.md)해야 합니다.


### Site Recovery로 어떤 워크로드를 보호할 수 있습니까?

Site Recovery를 사용하여 가상 컴퓨터 또는 물리적 서버에서 실행되는 대부분의 워크로드를 보호할 수 있습니다. Site Recovery는 응용 프로그램 인식 DR을 배포하는 데 도움이 됩니다. SharePoint, Exchange, Dynamics, SQL Server, Active Directory를 포함한 Microsoft 응용 프로그램과 통합되고, Oracle, SAP, IBM, Red Hat 등의 선두 공급 업체 제품과 긴밀하게 작동하므로 특정 응용 프로그램에 맞게 재해 복구 솔루션을 사용자 지정할 수 있습니다. 워크로드 보호에 대해 [자세히 알아보세요](site-recovery-workload.md).


### Hyper-V 가상 컴퓨터를 보호하려면 항상 System Center VMM 서버가 필요합니까? 

아니요. Azure에 복제하는 경우에는 VMM 클라우드에 있는/없는 Hyper-V 호스트 서버에 위치한 Hyper-V VM을 복제할 수 있습니다. 보조 데이터 센터에 복제하는 경우에는 VMM 클라우드에서 Hyper-V 호스트 서버를 관리해야 합니다. [자세히 알아보기](site-recovery-hyper-v-site-to-azure.md)

### VMM 서버가 하나밖에 없는 경우 VMM을 사용하여 Site Recovery를 배포할 수 있습니까? 

예. 클라우드의 Hyper-V VM을 VMM 서버에서 Azure로 복제하거나 같은 서버의 VMM 클라우드 간에 복제할 수 있습니다. 온-프레미스 간 복제는 기본 사이트와 보조 사이트에 VMM 서버가 있는 경우에 사용하는 것이 좋습니다. [자세히 알아보기](site-recovery-single-vmm.md)

### 어떤 물리적 서버를 보호할 수 있습니까?

Windows 및 Linux를 실행하는 물리적 서버를 Azure 또는 보조 사이트로 복제하여 보호할 수 있습니다. 운영 체제 요구 사항은 [무엇이 필요하나요?](site-recovery-vmware-to-azure.md/#what-do-i-need)를 참조하세요. 물리적 서버에서 Azure로 복사하든 아니면 보조 사이트로 복사하든 관계없이 동일한 제한 사항이 적용됩니다.

온-프레미스 서버가 중지되면 물리적 서버가 Azure에서 VM으로 실행됩니다. 온-프레미스 물리적 서버로의 장애 복구는 현재 지원되지 않지만 Hyper-V 또는 VMware에서 실행되는 가상 컴퓨터로의 장애 복구는 가능합니다.

### 어떤 VMware VM을 보호할 수 있습니까?

이 시나리오의 경우 VMware vCenter 서버, vSphere 하이퍼바이저 및 VMware 도구를 실행 중인 가상 컴퓨터가 필요합니다. 정확한 요구 사항은 [무엇이 필요하나요?](site-recovery-vmware-to-azure.md/#what-do-i-need)를 참조하세요. 물리적 서버에서 Azure로 복사하든 아니면 보조 사이트로 복사하든 관계없이 동일한 제한 사항이 적용됩니다.

### Azure에 가상 컴퓨터를 복제하기 위한 필수 조건은 무엇입니까?

Azure로 복제하려는 가상 컴퓨터가 [Azure 요구 사항](site-recovery-best-practices.md/#virtual-machines)을 충족해야 합니다.

### Hyper-V 2세대 가상 컴퓨터를 Azure로 복제할 수 있습니까?

예. 장애 조치(failover) 동안 Site Recovery가 컴퓨터를 2세대에서 1세대로 변환합니다. 장애 복구 시 컴퓨터가 다시 2세대로 변환됩니다. [자세히 알아보기](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Azure로 복제할 경우 Azure VM 요금을 어떻게 결제합니까? 

주기적 복제 동안 데이터가 지역 중복 저장소에 복제되므로 어떤 Azure IaaS 가상 컴퓨터 요금도 지불할 필요가 없습니다(큰 장점). Azure로 장애 조치(failover)를 수행하면 Site Recovery에서 자동으로 Azure IaaS 가상 컴퓨터를 만듭니다. 그 후 Azure에서 소비한 계산 리소스만큼 요금이 청구됩니다.

### Site Recovery로 지사의 재해 복구를 관리할 수 있습니까?

예. 지사에서 Site Recovery를 사용하여 복제를 오케스트레이션하고 장애 조치(failover)를 수행하면 중앙의 한 위치에 모든 지사 워크로드의 통합되지 않은 오케스트레이션 및 보기가 표시됩니다. 지사를 방문하지 않고 본사에서 간편하게 모든 지사의 장애 조치(failover)를 수행하고 재해 복구를 관리할 수 있습니다.

### ASR 워크플로 자동화에 사용할 수 있는 SDK가 있습니까?

예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. 자세한 내용은 [PowerShell을 사용하여 Site Recovery 배포](site-recovery-deploy-with-powershell.md)를 참조하세요.


## 보안 및 규정 준수

### 내 데이터가 Site Recovery 서비스로 전송되나요?

아니요, Site Recovery는 응용 프로그램 데이터를 가로채거나 사용자의 가상 컴퓨터 또는 물리적 서버에서 실행 중인 항목에 대한 정보를 보유하지 않습니다.

복제 데이터는 Hyper-V 호스트, VMware 하이퍼바이저 또는 기본 및 보조 데이터 센터의 물리적 서버 간에 교환되거나 데이터 센터와 Azure 저장소 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.

Site Recovery는 ISO 27001:2005 인증이며, HIPAA, DPA 및 FedRAMP JAB 평가를 완료하는 중입니다.

### 규정 준수를 위해 온-프레미스 환경의 메타데이터까지도 지리적으로 같은 지역에 남아 있어야 합니다. Site Recovery가 도움이 되나요?

예. 사용자가 한 지역에 Site Recovery 자격 증명 모음을 만들면 복제 및 장애 조치(failover)를 활성화하고 오케스트레이션하는 데 필요한 모든 메타데이터가 해당 지역의 지리적 경계 내에 남아 있습니다.

### Site Recovery는 복제를 암호화합니까?
온-프레미스 사이트 간에 가상 컴퓨터와 물리적 서버를 복제할 때 전송 중 암호화가 지원됩니다. 온-프레미스 사이트와 Azure 간에 가상 컴퓨터와 물리적 서버를 복제할 때 전송 중 암호화 및 Azure 내 암호화가 모두 지원됩니다.




## 복제 및 장애 조치(Failover)

### Azure로 복제할 때 어떤 유형의 저장소 계정이 필요합니까?

[표준 지역 중복 저장소](../storage/storage-redundancy.md/#geo-redundant-storage) 계정이 필요합니다. [프리미엄 저장소 계정](../storage/storage-premium-storage-preview-portal/)은 VMware 가상 컴퓨터 또는 Windows/Linux 물리적 서버를 Azure에 복제하는 경우에만 지원됩니다.

표준 로컬 중복 저장소에 대한 지원은 백로그에 있습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/7204469-local-redundant-type-azure-storage-support)을 통해 이 기능에 대한 의견을 보내 주세요.

### 데이터를 얼마나 자주 복제할 수 있나요?
- **Hyper-V:** Hyper-V VM을 30초, 5분 또는 15분마다 복제할 수 있습니다. SAN 복제를 설정하면 복제가 동기화됩니다.
- **VMware 및 물리적 서버:** 복제 빈도는 이와 관련이 없습니다. 복제는 지속적으로 수행됩니다. 

### 기존 복구 사이트에서 3차 사이트로 복제를 확장할 수 있습니까?
확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)을 통해 이 기능에 대한 의견을 보내 주세요.


### Azure에 처음으로 복제할 때 오프라인으로 복제할 수 있습니까? 

지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.


### 특정 디스크를 복제에서 제외할 수 있습니까?

지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication)을 통해 이 기능에 대한 의견을 보내 주세요.

### 동적 디스크를 사용하여 가상 컴퓨터를 복제할 수 있습니까?

동적 디스크는 Hyper-V 가상 컴퓨터를 복제할 때 지원됩니다. VMware 가상 컴퓨터 또는 물리적 서버를 복제할 때에는 지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery)을 통해 이 기능에 대한 의견을 보내 주세요.

### Azure로 장애 조치(Failover)하는 경우 장애 조치(Failover) 후에 어떻게 Azure 가상 컴퓨터에 액세스할 수 있습니까? 

보안 인터넷 연결 또는 사이트 간 VPN(또는 Azure Express 경로)이 있다면 이를 통해 Azure VM에 액세스할 수 있습니다. VPN 연결을 사용할 경우 VM이 있는 Azure 네트워크의 내부 포트와 통신이 이루어집니다. 인터넷을 통한 통신은 VM에 대한 Azure 클라우드 서비스의 공용 끝점에 매핑됩니다. [자세히 알아보기](site-recovery-network-design.md/#connectivity-after-failover)

### Azure로 장애 조치(Failover)하는 경우 Azure는 어떻게 데이터 복원을 보장합니까?

Azure는 서비스 복원을 위해 설계되었습니다. Site Recovery는 이미 Azure SLA를 따라 보조 Azure 데이터 센터에 대한 장애 조치(Failover)를 위해 엔지니어링되었습니다. 이 상황이 발생하면 사용자가 자격 증명 모음에 대해 선택한 지리적으로 동일한 지역에 메타데이터 및 자격 증명 모음이 남아 있습니다.

### 두 데이터 센터 간에 복제하는 동안 주 데이터 센터에서 예기치 않게 정전이 발생하면 어떻게 됩니까?

보조 사이트에서 계획되지 않은 장애 조치(Failover)를 트리거할 수 있습니다. Site Recovery는 기본 사이트가 연결되지 않아도 장애 조치(Failover)를 수행할 수 있습니다.


### 장애 조치(Failover)는 자동입니까?

자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치(Failover)를 시작하거나 [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx)을 사용하여 장애 조치(Failover)를 트리거할 수 있습니다. 장애 복구(failback) 작업 역시 Site Recovery 포털에서 간단하게 수행할 수 있습니다. 온-프레미스 Orchestrator 또는 Operations Manager를 사용하여 가상 컴퓨터 오류를 감지하면 SDK를 사용하여 장애 조치(Failover)를 트리거하도록 자동화할 수 있습니다.

### Hyper-V VM을 복제할 때 Hyper-V 복제 트래픽에 할당된 대역폭을 제한할 수 있습니까?
- Hyper-V VM과 두 온-프레미스 사이트 간에 복제를 수행하는 경우 Windows QoS를 사용할 수 있습니다. 다음은 샘플 스크립트입니다. 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Azure에 Hyper-V VM을 복제할 경우 다음 샘플 PowerShell cmdlet을 사용하여 제한을 구성할 수 있습니다.

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)



## 서비스 공급자 배포

### Site Recovery는 전용 또는 공유 인프라 모델에 대해 작동합니까?
예, Site Recovery는 전용 및 공유 인프라 모델 모두에 대해 작동합니다.

### 내 테넌트의 ID가 Site Recovery 서비스와 공유됩니까?
아니요. 사실 사용자의 테넌트는 Site Recovery 포털에 액세스할 필요가 없습니다. 서비스 공급자 관리자만 포털에서 작업을 수행합니다.


### 테넌트 응용 프로그램 데이터가 Azure로 이동될까요?
서비스 공급자가 소유하고 있는 사이트 간에 복제할 경우 응용 프로그램 데이터가 Azure로 이동되지 않습니다. 데이터는 전송 중에 암호화되어 서비스 공급자 사이트 간에 직접 복제됩니다.

Azure로 복제하는 경우 응용 프로그램 데이터가 Azure 저장소로 전송되지만 Site Recovery 서비스로는 전송되지 않습니다. 데이터는 전송 중에 암호화되어 Azure에 암호화된 상태로 남아 있습니다.

### 내 테넌트로 Azure 서비스에 대한 청구서가 발급됩니까?

아니요. Azure의 청구 관계는 서비스 공급자와 직접 유지됩니다. 서비스 공급자는 해당 테넌트에 대한 특정 청구서를 생성하는 일을 담당합니다.

### Azure로 복제할 때 항상 Azure에서 가상 컴퓨터를 실행해야 합니까?

아니요, 데이터가 자신의 구독에 있는 지역 중복 Azure 저장소 계정에 복제됩니다. 테스트 장애 조치(Failover)(DR 드릴) 또는 실제 장애 조치(Failover)를 수행하면 Site Recovery가 구독에서 자동으로 가상 컴퓨터를 만듭니다.

### Azure에 복제할 때 테넌트 수준의 격리가 보장되나요?

예.

### 현재 어떤 플랫폼이 지원됩니까?

Azure Pack, 클라우드 플랫폼 시스템 및 시스템 센터 기반(2012 이상)의 배포가 지원됩니다. Azure Pack 및 Site Recovery 통합에 대한 자세한 내용은 [가상 컴퓨터에 대한 보호 구성](https://technet.microsoft.com/library/dn850370.aspx)을 참조하세요.

### 단일 Azure Pack 및 단일 VMM 서버 배포가 지원되나요?

예, Hyper-V 가상 컴퓨터와 Azure 간에 또는 서비스 공급자 사이트 간에 복제할 수 있습니다. 서비스 공급자 사이트 간에 복제할 경우 Azure Runbook 통합을 사용할 수 없습니다.


## 다음 단계

- [Site Recovery 개요](site-recovery-overview.md) 보기
- [Site Recovery 아키텍처](site-recovery-components.md)에 대해 알아보기  

 

<!---HONumber=AcomDC_1210_2015-->