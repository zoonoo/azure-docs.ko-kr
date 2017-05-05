---
title: "Azure Site Recovery: 질문과 대답(FAQ) | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery에 대한 일반적인 질문에 대답합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: get-started-article
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 63f2cf1617bf9c265273c6a8e91703542b9eb8d4
ms.lasthandoff: 04/27/2017


---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: FAQ(질문과 대답)
이 문서는 Azure Site Recovery에 대한 질문과 대답을 제공합니다. 이 문서를 읽은 후 질문이 있다면 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 게시해 주세요.

## <a name="general"></a>일반
### <a name="what-does-site-recovery-do"></a>Site Recovery의 기능은 무엇입니까?
Site Recovery는 온-프레미스 가상 컴퓨터 및 물리적 서버에서 Azure 또는 보조 데이터 센터로의 복제 작업을 오케스트레이션 및 자동화하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. [자세히 알아보세요](site-recovery-overview.md)을 확인하세요.

### <a name="what-can-site-recovery-protect"></a>Site Recovery로 무엇을 보호할 수 있습니까?
* **VMware 가상 컴퓨터**: Site Recovery는 Hyper-V VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다.
* **물리적 서버**: Site Recovery는 Windows 또는 Linux를 실행하는 물리적 서버를 보호할 수 있습니다.
* **VMware 가상 컴퓨터**: Site Recovery는 VMware VM에서 실행 중인 모든 워크로드를 보호할 수 있습니다.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Site Recovery는 Azure Resource Manager 모델을 지원합니까?
Azure 클래식 포털의 Site Recovery 외에도 Resource Manager에 대한 지원을 통해 Azure 포털에서 Site Recovery를 사용할 수 있습니다. 대부분의 배포 시나리오에서 Azure 포털의 Site Recovery는 간소화된 배포 경험을 제공하며 VM 및 물리적 서버를 클래식 저장소 또는 Resource Manager 저장소에 복제할 수 있습니다. 지원되는 배포는 다음과 같습니다.

* [Azure 포털에서 VMware VM 또는 물리적 서버를 Azure로 복제](site-recovery-vmware-to-azure.md)
* [Azure 포털에서 VMM 클라우드의 Hyper-V VM을 Azure로 복제](site-recovery-vmm-to-azure.md)
* [Azure 포털에서 (VMM 없이) Hyper-V VM을 Azure로 복제](site-recovery-hyper-v-site-to-azure.md)
* [Azure 포털에서 VMM 클라우드의 Hyper-V VM을 보조 사이트로 복제](site-recovery-vmm-to-vmm.md)

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Hyper-V에서 Site Recovery를 통해 복제를 오케스트레이션하기 위해 필요한 것은 무엇입니까?
Hyper-V 호스트 서버에서 필요한 사항은 배포 시나리오에 따라 달라집니다. Hyper-V 필수 구성 요소를 확인해 보세요.

* [Azure로 Hyper-V VM 복제(VMM 없이)](site-recovery-hyper-v-site-to-azure.md)
* [Azure로 Hyper-V VM 복제(VMM 사용)](site-recovery-vmm-to-azure.md)
* [보조 데이터 센터에 Hyper-V VM 복제](site-recovery-vmm-to-vmm.md)
* 보조 데이터 센터에 복제하는 경우 [Hyper-V VM에 대해 지원되는 게스트 운영 체제](https://technet.microsoft.com/library/mt126277.aspx)에 대해 알아보세요.
* Azure에 복제하는 경우에는 [Azure에서 지원되는](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)모든 게스트 운영 체제를 Site Recovery에서도 지원합니다.

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Hyper-V가 클라이언트 운영 체제에서 실행 중인 경우 VM을 보호할 수 있습니까?
아니요, VM은 지원되는 Windows 서버 컴퓨터를 실행하는 Hyper-V 호스트 서버에 위치해야 합니다. 클라이언트 컴퓨터를 보호해야 하는 경우 물리적 컴퓨터를 [Azure](site-recovery-vmware-to-azure.md) 또는 [보조 데이터 센터](site-recovery-vmware-to-vmware.md)로 복제할 수 있습니다.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Site Recovery로 어떤 워크로드를 보호할 수 있습니까?
Site Recovery를 사용하여 지원되는 VM 또는 물리적 서버에서 실행되는 대부분의 워크로드를 보호할 수 있습니다. Site Recovery는 응용 프로그램 인식 복제를 제공하므로 앱을 지능형 상태로 복구할 수 있습니다. SharePoint, Exchange, Dynamics, SQL Server, Active Directory와 같은 Microsoft 응용 프로그램과 통합되고, Oracle, SAP, IBM, Red Hat 등의 선두 공급 업체 제품과 긴밀하게 작동합니다. [자세히 알아봅니다](site-recovery-workload.md) .

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hyper-V 호스트가 VMM 클라우드에 있어야 합니까?
보조 데이터 센터에 복제하려는 경우 Hyper-V VM은 VMM 클라우드에 위치한 Hyper-V 호스트 서버에 있어야 합니다. Azure로 복제하려는 경우 VMM 클라우드 존재 여부와 상관없이 Hyper-V 호스트 서버에서 VM을 복제할 수 있습니다. [자세히 알아보기](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>VMM 서버가 하나밖에 없는 경우 VMM을 사용하여 Site Recovery를 배포할 수 있습니까?

예. VMM 클라우드의 Hyper-V 서버에서 VM을 Azure로 복제하거나 같은 서버의 VMM 클라우드 간에 복제할 수 있습니다. 온-프레미스 간 복제는 기본 사이트와 보조 사이트에 VMM 서버가 있는 경우에 사용하는 것이 좋습니다.  

### <a name="what-physical-servers-can-i-protect"></a>어떤 물리적 서버를 보호할 수 있습니까?
Windows 및 Linux를 실행하는 물리적 서버를 Azure 또는 보조 사이트로 복제할 수 있습니다. [알아봅니다](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) .  물리적 서버에서 Azure로 복사하든 아니면 보조 사이트로 복사하든 관계없이 동일한 요구 사항이 적용됩니다.


온-프레미스 서버가 중지되면 물리적 서버가 Azure에서 VM으로 실행됩니다. 온-프레미스 물리적 서버로의 장애 복구(failback)는 현재 지원되지 않습니다. 물리적으로 보호되는 컴퓨터의 경우 VMware 가상 컴퓨터로만 장애 복구(failback)할 수 있습니다.

### <a name="what-vmware-vms-can-i-protect"></a>어떤 VMware VM을 보호할 수 있습니까?

VMware VM을 보호하려면 vSphere 하이퍼바이저 및 VMware 도구를 실행 중인 가상 컴퓨터가 필요합니다. 하이퍼바이저를 관리하는 VMware vCenter 서버가 있는 것이 좋습니다. VMware 서버 및 VM을 Azure 또는 보조 사이트로 복제하기 위한 정확한 요구 사항에 대해 [자세히 알아보세요](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Site Recovery로 지사의 재해 복구를 관리할 수 있습니까?
예. 지사에서 Site Recovery를 사용하여 복제를 오케스트레이션하고 장애 조치(failover)를 수행하면 중앙의 한 위치에 모든 지사 워크로드의 통합되지 않은 오케스트레이션 및 보기가 표시됩니다. 지사를 방문하지 않고 본사에서 간편하게 모든 지사의 장애 조치(failover)를 수행하고 재해 복구를 관리할 수 있습니다.

## <a name="pricing"></a>가격

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>Azure Site Recovery를 사용하는 동안 어떤 요금이 발생하나요?
Site Recovery를 사용하는 경우 Site Recovery 라이선스, Azure 저장소, 저장소 트랜잭션 및 아웃바운드 데이터 전송에 대한 요금이 발생합니다. [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery)을 확인하세요.

Site Recovery 라이선스는 보호된 인스턴스당 요금이 청구되며, 여기서 인스턴스는 VM 또는 물리적 서버를 말합니다.

- VM 디스크가 표준 저장소 계정에 복제하면 사용한 저장소만큼 Azure 저장소 비용이 청구됩니다. 예를 들어 원본 디스크 크기가 1TB이고, 400GB를 사용 중이고, Site Recovery가 Azure에 1TB VHD를 만드는 경우 400GB(그리고 복제 로그에 사용된 저장소 공간의 크기)에 대한 요금이 청구됩니다.
- VM 디스크가 프리미엄 저장소 계정에 복제하는 경우 프로비전된 저장소 크기를 가장 가까운 Premium Storage 디스크 옵션으로 반올림하여 Azure 저장소 요금이 산정됩니다. 예를 들어 원본 디스크 크기가 50GB이고 Site Recovery가 Azure에 50GB를 만들면 Azure가 이를 가장 가까운 Premium Storage 디스크(P10)로 매핑합니다.  비용은 50GB 디스크 크기가 아닌 P10을 기준으로 산정됩니다.  [자세히 알아보세요](https://aka.ms/premium-storage-pricing)을 확인하세요.  Premium Storage를 사용하는 경우 복제 로깅을 위한 표준 저장소 계정도 필요하며, 이러한 로그에 사용되는 표준 저장소 공간의 양만큼 요금이 청구됩니다.

테스트 장애 조치(failover)를 수행하는 동안에도 비용이 발생하며 VM, 저장소, 전송 및 저장소 트랜잭션 비용이 적용됩니다.



## <a name="security"></a>보안
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>복제 데이터가 사이트 복구 서비스로 전송되나요?
아니요, Site Recovery는 복제된 데이터를 가로채거나 사용자의 가상 컴퓨터 또는 물리적 서버에서 실행 중인 항목에 대한 정보를 보유하지 않습니다.
복제 데이터는 온-프레미스 Hyper-V 호스트, VMware 하이퍼바이저 또는 물리적 서버와 Azure 저장소 또는 보조 사이트 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA 인증을 받았으며, SOC2 및 FedRAMP JAB 평가를 수행하는 중입니다.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>규정 준수를 위해 온-프레미스 메타데이터까지도 지리적으로 같은 지역에 남아 있어야 합니다. Site Recovery가 도움이 되나요?
예. 사용자가 한 지역에 Site Recovery 자격 증명 모음을 만들면 복제 및 장애 조치(failover)를 활성화하고 오케스트레이션하는 데 필요한 모든 메타데이터가 해당 지역의 지리적 경계 내에 남아 있습니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?
온-프레미스 사이트 간에 가상 컴퓨터와 물리적 서버를 복제할 때 전송 중 암호화가 지원됩니다. Azure에 복제되는 가상 컴퓨터와 물리적 서버의 경우 전송 중 암호화 및 정지된 암호화(Azure 내)가 모두 지원됩니다.

## <a name="replication"></a>복제

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>사이트 간 VPN을 통해 Azure에 복제할 수 있습니까?
Azure Site Recovery는 공용 끝점을 통해 Azure 저장소 계정에 데이터를 복제합니다. 사이트 간 VPN을 통해 복제되지 않습니다. Azure 가상 네트워크를 사용하여 사이트 간 VPN을 만들 수 있습니다. Site Recovery 복제를 방해하지 않습니다.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>ExpressRoute를 사용하여 가상 컴퓨터를 Azure로 복제할 수 있습니까?
예, ExpressRoute를 사용하여 가상 컴퓨터를 Azure로 복제할 수 있습니다. Azure Site Recovery는 공용 끝점을 통해 Azure Storage 계정에 데이터를 복제합니다. Site Recovery 복제에 ExpressRoute를 사용하려면 [공용 피어링](../expressroute/expressroute-circuit-peerings.md#public-peering)을 설정해야 합니다. 가상 컴퓨터가 Azure Virtual Network에 장애 조치 된 후 Azure Virtual Network로 [개인 피어링](../expressroute/expressroute-circuit-peerings.md#private-peering) 설정을 사용하여 액세스할 수 있습니다.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Azure에 가상 컴퓨터를 복제하기 위한 필수 조건은 무엇입니까?
Azure로 복제하려는 가상 컴퓨터가 [Azure 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 충족해야 합니다.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Hyper-V 2세대 가상 컴퓨터를 Azure로 복제할 수 있습니까?
예. 장애 조치(failover) 동안 Site Recovery가 컴퓨터를 2세대에서 1세대로 변환합니다. 장애 복구 시 컴퓨터가 다시 2세대로 변환됩니다. [자세히 알아보기](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Azure로 복제할 경우 Azure VM 요금을 어떻게 결제합니까?
주기적 복제 동안 데이터가 지역 중복 저장소에 복제되므로 어떤 Azure IaaS 가상 컴퓨터 요금도 지불할 필요가 없습니다(큰 장점). Azure로 장애 조치(failover)를 수행하면 Site Recovery에서 자동으로 Azure IaaS 가상 컴퓨터를 만듭니다. 그 후 Azure에서 소비한 계산 리소스만큼 요금이 청구됩니다.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>SDK와 함께 Site Recovery 시나리오를 자동화할 수 있습니까?
예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. 현재 PowerShell을 사용하여 Site Recovery를 배포하기 위한 지원되는 시나리오:

* [VMM 클라우드의 Hyper-V VM을 Azure PowerShell Resource Manager로 복제](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Hyper-V VM을 VMM 없이 Azure PowerShell Resource Manager로 복제](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Azure로 복제할 때 어떤 유형의 저장소 계정이 필요합니까?
* **Azure 클래식 포털**: Azure 클래식 포털에서 Site Recovery를 배포하는 경우 [표준 지역 중복 저장소 계정](../storage/storage-redundancy.md#geo-redundant-storage)이 있어야 합니다. 프리미엄 저장소는 현재 지원되지 않습니다. 계정은 Site Recovery 자격 증명 모음과 동일한 지역에 있어야 합니다.
* **Azure Portal**: Azure Portal에서 Site Recovery를 배포하는 경우 LRS 또는 GRS 저장소 계정이 있어야 합니다. 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 GRS를 사용하는 것이 좋습니다. 계정은 복구 서비스 자격 증명 모음과 동일한 지역에 있어야 합니다. 프리미엄 저장소는 이제 Azure Portal에서 Site Recovery를 배포할 때 VMware VM, Hyper-V VM 및 물리적 서버 복제에 지원됩니다.

### <a name="how-often-can-i-replicate-data"></a>데이터를 얼마나 자주 복제할 수 있나요?
* **Hyper-V:** Hyper-V VM은 30초(프리미엄 저장소 제외), 5분 또는 15분마다 복제할 수 있습니다. SAN 복제를 설정하면 복제가 동기화됩니다.
* **VMware 및 물리적 서버:** 복제 빈도는 이와 관련이 없습니다. 복제가 계속 됩니다.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>기존 복구 사이트에서 3차 사이트로 복제를 확장할 수 있습니까?
확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Azure에 처음으로 복제할 때 오프라인으로 복제할 수 있습니까?
지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-exclude-specific-disks-from-replication"></a>특정 디스크를 복제에서 제외할 수 있습니까?
이는 Azure Portal을 사용하여 Azure에 [VMware VM 및 Hyper-V VM을 복제](site-recovery-exclude-disk.md)하는 경우에만 지원됩니다.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>동적 디스크를 사용하여 가상 컴퓨터를 복제할 수 있습니까?
동적 디스크는 Hyper-V 가상 컴퓨터를 복제할 때 지원됩니다. 또한 VMware VM 및 물리적 컴퓨터를 Azure에 복제할 때도 지원됩니다. 운영 체제 디스크는 기본 디스크여야 합니다.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>기존 복제 그룹에 새 컴퓨터를 추가할 수 있습니까?
기존 복제 그룹에 새 컴퓨터를 추가할 수 있습니다. 이렇게 하려면 '복제된 항목' 블레이드에서 복제 그룹을 선택하고 복제 그룹에 대한 상황에 맞는 메뉴를 마우스 오른쪽 단추로 클릭/선택한 다음 적절한 옵션을 선택합니다.

![복제 그룹에 추가](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Hyper-V 복제 트래픽에 할당된 대역폭을 제한할 수 있습니까?
예. 배포 문서에서 대역폭 제한에 대해 더 자세히 검토할 수 있습니다.

* [VMware VM 및 물리적 서버를 복제하기 위한 용량 계획](site-recovery-plan-capacity-vmware.md)
* [을 복제하기 위한 용량 계획](site-recovery-vmm-to-azure.md#capacity-planning)
* [VMM 없이 Hyper-V VM을 복제하기 위한 용량 계획](site-recovery-hyper-v-site-to-azure.md#capacity-planning)

## <a name="failover"></a>장애 조치(Failover)
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Azure로 장애 조치(Failover)하는 경우 장애 조치(Failover) 후에 어떻게 Azure 가상 컴퓨터에 액세스할 수 있습니까?
보안 인터넷 연결 또는 사이트 간 VPN 또는 Azure Express 경로로 Azure VM에 액세스할 수 있습니다. 연결하려면 여러 가지 사항을 준비해야 합니다. [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Azure로 장애 조치(Failover)하는 경우 Azure는 어떻게 데이터 복원을 보장합니까?
Azure는 복원을 위해 디자인되었습니다. Site Recovery는 이미 Azure SLA를 따라 보조 Azure 데이터 센터에 대한 장애 조치(Failover)를 위해 엔지니어링되었습니다. 이 상황이 발생하면 사용자가 자격 증명 모음에 대해 선택한 지리적으로 동일한 지역에 메타데이터 및 자격 증명 모음이 남아 있습니다.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>두 데이터 센터 간에 복제하는 동안 주 데이터 센터에서 예기치 않게 정전이 발생하면 어떻게 됩니까?
보조 사이트에서 계획되지 않은 장애 조치(Failover)를 트리거할 수 있습니다. Site Recovery는 기본 사이트가 연결되지 않아도 장애 조치(Failover)를 수행할 수 있습니다.

### <a name="is-failover-automatic"></a>장애 조치(Failover)는 자동입니까?
자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치(Failover)를 시작하거나 [Site Recovery PowerShell](/powershell/module/azurerm.siterecovery) 을 사용하여 장애 조치(Failover)를 트리거할 수 있습니다. 장애 복구(failback) 작업은 Site Recovery 포털에서 간단하게 수행할 수 있습니다.

온-프레미스 Orchestrator 또는 Operations Manager를 사용하여 가상 컴퓨터 오류를 감지하면 SDK를 사용하여 장애 조치(Failover)를 트리거하도록 자동화할 수 있습니다.

* [자세한](site-recovery-create-recovery-plans.md) 복구 계획을 알아봅니다.
* [자세히 알아보기](site-recovery-failover.md) .
* [자세히 알아보기](site-recovery-failback-azure-to-vmware.md) 

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>내 온-프레미스 호스트가 응답하지 않거나 손상된 경우 다른 호스트로 장애 조치(failover)할 수 있나요?
예, 대체 위치 복구를 사용하여 Azure에서 다른 호스트로 장애 복구(failback)를 수행할 수 있습니다. VMware 및 Hyper-V 가상 컴퓨터에 대한 옵션을 자세히 알아보려면 아래 링크를 참조하세요.

* [VMware 가상 컴퓨터](site-recovery-how-to-failback-azure-to-vmware.md#fail-back-to-the-original-or-alternate-location)
* [Hyper-V 가상 컴퓨터](site-recovery-failback-from-azure-to-hyper-v.md#failback-to-an-alternate-location)

## <a name="service-providers"></a>서비스 공급자
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>서비스 공급자입니다. Site Recovery는 전용 및 공유 인프라 모델에 대해 작동합니까?
예, Site Recovery는 전용 및 공유 인프라 모델 모두에 대해 작동합니다.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>서비스 공급자의 경우 내 테넌트의 ID가 Site Recovery 서비스와 공유됩니까?
아니요. 테넌트 ID는 익명으로 유지됩니다. 사용자의 테넌트는 사이트 복구 포털에 액세스할 필요가 없습니다. 서비스 공급자 관리자만 포털과 상호 작용합니다.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>테넌트 응용 프로그램 데이터가 Azure로 이동됩니까?
서비스 공급자가 소유하고 있는 사이트 간에 복제할 경우 응용 프로그램 데이터가 Azure로 이동되지 않습니다. 데이터는 전송 중에 암호화되어 서비스 공급자 사이트 간에 직접 복제됩니다.

Azure로 복제하는 경우 응용 프로그램 데이터가 Azure 저장소로 전송되지만 Site Recovery 서비스로는 전송되지 않습니다. 데이터는 전송 중에 암호화되어 Azure에 암호화된 상태로 남아 있습니다.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>내 테넌트로 Azure 서비스에 대한 청구서가 발급됩니까?
아니요. Azure의 청구 관계는 서비스 공급자와 직접 유지됩니다. 서비스 공급자는 해당 테넌트에 대한 특정 청구서를 생성하는 일을 담당합니다.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Azure로 복제할 때 항상 Azure에서 가상 컴퓨터를 실행해야 합니까?
아니요, 데이터는 구독의 Azure 저장소 계정에 복제됩니다. 테스트 장애 조치(Failover)(DR 드릴) 또는 실제 장애 조치(Failover)를 수행하면 Site Recovery가 구독에서 자동으로 가상 컴퓨터를 만듭니다.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Azure에 복제할 때 테넌트 수준의 격리가 보장되나요?
예.

### <a name="what-platforms-do-you-currently-support"></a>현재 어떤 플랫폼이 지원됩니까?
Azure Pack, 클라우드 플랫폼 시스템 및 시스템 센터 기반(2012 이상)의 배포가 지원됩니다. [자세히 알아봅니다](https://technet.microsoft.com/library/dn850370.aspx) .

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>단일 Azure Pack 및 단일 VMM 서버 배포가 지원되나요?
예, Hyper-V 가상 컴퓨터를 Azure에 복제하거나 서비스 공급자 사이트 간에 복제할 수 있습니다.  서비스 공급자 사이트 간에 복제할 경우 Azure Runbook 통합을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [Site Recovery 개요](site-recovery-overview.md)
* 알아봅니다 [Site Recovery 아키텍처](site-recovery-components.md)  

