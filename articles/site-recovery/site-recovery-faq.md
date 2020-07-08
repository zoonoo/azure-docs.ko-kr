---
title: Azure Site Recovery 서비스에 대한 일반적인 질문
description: 이 문서에서는 Azure Site Recovery에 대한 일반적인 주요 질문에 대답합니다.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: 9eceb9643a5e8f8eab6b68bb04b322a099b715f3
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057435"
---
# <a name="general-questions-about-azure-site-recovery"></a>Azure Site Recovery에 대한 일반적인 질문

이 문서는 Azure Site Recovery에 대한 질문과 대답을 요약합니다. 특정 시나리오의 경우 다음 문서 검토

- [Azure로 Azure VM 재해 복구에 대한 질문](azure-to-azure-common-questions.md)
- [Azure로 VMware VM 재해 복구에 대한 질문](vmware-azure-common-questions.md)
- [Azure로 Hyper-V VM 재해 복구에 대한 질문](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>일반

### <a name="what-does-site-recovery-do"></a>Site Recovery의 기능은 무엇입니까?

Site Recovery는 온-프레미스 가상 머신 및 물리적 서버에서 Azure로, 그리고 온-프레미스 컴퓨터에서 보조 데이터 센터로의 지역 간 Azure VM 복제 작업을 오케스트레이션 및 자동화하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. [자세히 알아보기](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Docker 디스크가 있는 가상 머신을 보호할 수 있나요?

아니요, 이것은 지원되지 않는 시나리오입니다.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>데이터 무결성을 보장 하기 위해 Site Recovery 하는 작업은 무엇 인가요?

데이터 무결성을 보장 하기 위해 Site Recovery에서 수행 하는 다양 한 측정값이 있습니다. HTTPS 프로토콜을 사용 하 여 모든 서비스 간에 보안 연결이 설정 됩니다. 이렇게 하면 모든 맬웨어 또는 외부 엔터티가 데이터를 조작할 수 없습니다. 사용 되는 또 다른 측정값은 체크섬을 사용 하는 것입니다. 원본 및 대상 간의 데이터 전송은 데이터의 체크섬을 계산 하 여 실행 됩니다. 이렇게 하면 전송 된 데이터가 일관 되 게 유지 됩니다.

## <a name="service-providers"></a>서비스 공급자

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>서비스 공급자입니다. Site Recovery는 전용 및 공유 인프라 모델에 대해 작동합니까?
예, Site Recovery는 전용 및 공유 인프라 모델 모두에 대해 작동합니다.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>서비스 공급자의 경우 내 테넌트의 ID가 Site Recovery 서비스와 공유됩니까?
아니요. 테넌트 ID는 익명으로 유지됩니다. 사용자의 테넌트는 Site Recovery 포털에 액세스할 필요가 없습니다. 서비스 공급자 관리자만 포털과 상호 작용합니다.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>테넌트 애플리케이션 데이터가 Azure로 이동됩니까?
서비스 공급자가 소유하고 있는 사이트 간에 복제할 경우 애플리케이션 데이터가 Azure로 이동되지 않습니다. 데이터는 전송 중에 암호화되어 서비스 공급자 사이트 간에 직접 복제됩니다.

Azure로 복제하는 경우 애플리케이션 데이터가 Azure Storage로 전송되지만 Site Recovery 서비스로는 전송되지 않습니다. 데이터는 전송 중에 암호화되어 Azure에 암호화된 상태로 남아 있습니다.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>내 테넌트로 Azure 서비스에 대한 청구서가 발급됩니까?
아니요. Azure의 청구 관계는 서비스 공급자와 직접 유지됩니다. 서비스 공급자는 해당 테넌트에 대한 특정 청구서를 생성하는 일을 담당합니다.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Azure로 복제할 때 항상 Azure에서 가상 머신을 실행해야 합니까?
아니요, 데이터는 구독의 Azure Storage에 복제됩니다. 테스트 장애 조치(failover)(DR 드릴) 또는 실제 장애 조치(failover)를 수행하면 Site Recovery가 구독에서 자동으로 가상 머신을 만듭니다.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Azure에 복제할 때 테넌트 수준의 격리가 보장되나요?
예.

### <a name="what-platforms-do-you-currently-support"></a>현재 어떤 플랫폼이 지원됩니까?
Azure Pack, 클라우드 플랫폼 시스템 및 시스템 센터 기반(2012 이상)의 배포가 지원됩니다. [자세히 알아봅니다](https://technet.microsoft.com/library/dn850370.aspx) .

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>단일 Azure Pack 및 단일 VMM 서버 배포가 지원되나요?
예, Hyper-V 가상 머신을 Azure에 복제하거나 서비스 공급자 사이트 간에 복제할 수 있습니다.  서비스 공급자 사이트 간에 복제할 경우 Azure Runbook 통합을 사용할 수 없습니다.

## <a name="pricing"></a>가격 책정

### <a name="where-can-i-find-pricing-information"></a>가격 책정 정보는 어디에서 찾을 수 있나요?
[Site Recovery 가격 정보](https://azure.microsoft.com/pricing/details/site-recovery/) 세부 사항을 검토하세요.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Site Recovery 사용 중 대략적인 요금을 계산하려면 어떻게 하나요?

Site Recovery를 사용하는 동안 [가격 계산기](https://aka.ms/asr_pricing_calculator)를 사용하여 비용을 예측할 수 있습니다.

자세한 예상 비용을 보려면 배포 플래너 도구[VMware](https://aka.ms/siterecovery_deployment_planner) 또는 [Hyper-V](https://aka.ms/asr-deployment-planner)용 배포 플래너 도구를 실행하고 [비용 예상 보고서](https://aka.ms/asr_DP_costreport)를 사용합니다.


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>이제 VMware VM 및 물리적 서버를 복제하는 데 관리 디스크를 사용합니다. 관리 디스크를 사용하는 캐시 스토리지 계정에 대해 추가 요금이 발생하나요?

아니요, 캐시에 대한 추가 요금은 없습니다. 표준 스토리지 계정에 복제하는 경우 이 캐시 스토리지는 같은 대상 스토리지 계정에 속합니다.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>한 달 이상 Azure Site Recovery를 사용해 왔습니다. 그런데도 모든 보호된 인스턴스를 처음 31일 동안 무료로 사용할 수 있나요?

예. 모든 보호된 인스턴스에는 처음 31일 동안 Azure Site Recovery 요금이 부과되지 않습니다. 예를 들어 지난 6개월 동안 인스턴스 10개를 보호한 상태에서 11번째 인스턴스를 Azure Site Recovery에 연결하는 경우, 처음 31일 동안에는 11번째 인스턴스에 대한 요금이 부과되지 않습니다. 기존 인스턴스 10개는 31일이 넘게 보호되었기 때문에 Azure Site Recovery 요금이 계속 부과됩니다.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>처음 31일 동안 다른 Azure 요금이 부과되나요?

예, 보호된 인스턴스를 처음으로 사용하는 31일 동안 Site Recovery는 무료이지만, Azure Storage, 스토리지 트랜잭션 및 데이터 전송 요금이 부과될 수 있습니다. 복구된 가상 머신도 Azure Compute 요금이 발생할 수 있습니다.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>재해 복구 드릴/테스트 장애 조치(failover) 수행과 관련된 비용이 있나요?

DR 드릴에 대한 별도의 비용은 없습니다. 테스트 장애 조치(failover) 후 VM을 만들면 컴퓨팅 요금이 있습니다.



## <a name="security"></a>보안

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>복제 데이터가 Site Recovery 서비스로 전송되나요?
아니요, Site Recovery는 복제된 데이터를 가로채거나 사용자의 가상 머신 또는 물리적 서버에서 실행 중인 항목에 대한 정보를 보유하지 않습니다.
복제 데이터는 온-프레미스 Hyper-V 호스트, VMware 하이퍼바이저 또는 물리적 서버와 Azure Storage 또는 보조 사이트 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA 인증을 받았으며, SOC2 및 FedRAMP JAB 평가를 수행하는 중입니다.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>규정 준수를 위해 온-프레미스 메타데이터까지도 지리적으로 같은 지역에 남아 있어야 합니다. Site Recovery가 도움이 되나요?
예. 사용자가 한 지역에 Site Recovery 자격 증명 모음을 만들면 복제 및 장애 조치(failover)를 활성화하고 오케스트레이션하는 데 필요한 모든 메타데이터가 해당 지역의 지리적 경계 내에 남아 있습니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?
온-프레미스 사이트 간에 가상 머신과 물리적 서버를 복제할 때 전송 중 암호화가 지원됩니다. Azure에 복제되는 가상 머신과 물리적 서버의 경우 전송 중 암호화 및 [정지된 암호화(Azure 내)](https://docs.microsoft.com/azure/storage/storage-service-encryption)가 모두 지원됩니다.

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Azure-Azure Site Recovery에서 Azure 마이크로서비스를 거치는 모든 통신에 TLS 1.2를 사용하나요?
예, TLS 1.2 프로토콜은 Azure-Azure Site Recovery 시나리오에서 기본적으로 적용됩니다. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>VMware-Azure 및 물리적 서버-Azure Site Recovery 시나리오에서 TLS 1.2를 적용하려면 어떻게 해야 하나요?
복제된 항목에 설치된 모바일 에이전트는 TLS 1.2에서만 프로세스 서버로 통신합니다. 그러나 구성 서버에서 Azure로, 프로세스 서버에서 Azure로의 통신은 TLS 1.1 또는 1.0 기반일 수 있습니다. [지침](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)에 따라 사용자가 설정한 모든 구성 서버 및 프로세스 서버에 TLS 1.2를 적용하세요.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>HyperV-Azure Site Recovery 시나리오에서 TLS 1.2를 적용하려면 어떻게 해야 하나요?
Azure Site Recovery 마이크로서비스 간의 모든 통신은 TLS 1.2 프로토콜에서 발생합니다. Site Recovery는 시스템(OS)에 구성된 보안 공급자와, 사용 가능한 최신 TLS 프로토콜을 사용합니다. 레지스트리에서 TLS 1.2를 사용하도록 명시적으로 설정해야 합니다. 그러면 Site Recovery가 서비스와의 통신에 TLS 1.2를 사용합니다. 

## <a name="disaster-recovery"></a>재해 복구

### <a name="what-can-site-recovery-protect"></a>Site Recovery로 무엇을 보호할 수 있습니까?
* **Azure VM**: Site Recovery는 지원되는 Azure VM에서 실행 중인 모든 워크로드를 복제할 수 있습니다.
* **Hyper-V 가상 머신**: Site Recovery는 Hyper-V VM에서 실행되는 모든 워크로드를 보호할 수 있습니다.
* **실제 서버**: Site Recovery는 Windows 또는 Linux를 실행하는 실제 서버를 보호할 수 있습니다.
* **VMware 가상 머신**: Site Recovery는 VMware VM에서 실행되는 모든 워크로드를 보호할 수 있습니다.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Site Recovery로 어떤 워크로드를 보호할 수 있습니까?
Site Recovery를 사용하여 지원되는 VM 또는 물리적 서버에서 실행되는 대부분의 워크로드를 보호할 수 있습니다. Site Recovery는 애플리케이션 인식 복제를 제공하므로 앱을 지능형 상태로 복구할 수 있습니다. SharePoint, Exchange, Dynamics, SQL Server, Active Directory와 같은 Microsoft 애플리케이션과 통합되고, Oracle, SAP, IBM, Red Hat 등의 선두 공급 업체 제품과 긴밀하게 작동합니다. [자세히 알아봅니다](site-recovery-workload.md) .

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Site Recovery로 지사의 재해 복구를 관리할 수 있습니까?
예. 지사에서 Site Recovery를 사용하여 복제를 오케스트레이션하고 장애 조치(failover)를 수행하면 중앙의 한 위치에 모든 지사 워크로드의 통합되지 않은 오케스트레이션 및 보기가 표시됩니다. 지사를 방문하지 않고 본사에서 간편하게 모든 지사의 장애 조치(failover)를 수행하고 재해 복구를 관리할 수 있습니다.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Azure VM에 재해 복구가 지원되나요?

예, Site Recovery는 Azure 지역 간 Azure VM에 대해 재해 복구를 지원합니다. Azure VM 재해 복구에 대한 [일반적인 질문을 검토하세요](azure-to-azure-common-questions.md). 동일한 대륙의 두 Azure 지역 간에 복제 하려면 azure DR 제품을 사용 하세요. 구성 서버/프로세스 서버 및 Express 경로 연결을 설정할 필요가 없습니다.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>VMware VM에 재해 복구가 지원되나요?

예, Site Recovery는 온-프레미스 VMware VM의 재해 복구를 지원합니다. VMware 재해 복구에 대한 [일반적인 질문을 검토하세요](vmware-azure-common-questions.md).

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Hyper-V VM에 재해 복구가 지원되나요?
예, Site Recovery는 온-프레미스 Hyper-V VM의 재해 복구를 지원합니다. Hyper-V VM 재해 복구에 대한 [일반적인 질문을 검토하세요](hyper-v-azure-common-questions.md).

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>물리적 서버에 대한 재해 복구가 지원되나요?
예, Site Recovery는 Windows 및 Linux를 실행하는 온-프레미스 물리적 서버에서 Azure 또는 보조 사이트로의 재해 복구를 지원합니다. [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) 및 [보조 사이트](vmware-physical-secondary-support-matrix.md#replicated-vm-support)로의 재해 복구를 위한 요구 사항에 대해 알아보세요.
물리적 서버는 장애 조치(failover) 후 Azure에서 VM으로 실행됩니다. Azure에서 온-프레미스 물리적 서버로의 장애 복구(failback)는 현재 지원되지 않습니다. VMware 가상 머신으로만 장애 복구(failback)할 수 있습니다.





## <a name="replication"></a>복제

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>사이트 간 VPN을 통해 Azure에 복제할 수 있습니까?
Azure Site Recovery는 퍼블릭 엔드포인트를 통해 Azure Storage 계정 또는 관리 디스크에 데이터를 복제합니다. 사이트 간 VPN을 통해 복제되지 않습니다. 

### <a name="why-cant-i-replicate-over-vpn"></a>VPN을 통해 복제할 수 없는 이유는 무엇인가요?

Azure에 복제할 경우 복제 트래픽이 Azure Storage의 퍼블릭 엔드포인트에 도달합니다. 따라서 퍼블릭 인터넷이나 ExpressRoute(Microsoft 피어링 또는 기존 퍼블릭 피어링)를 통해서만 복제할 수 있습니다.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>복제에 Riverbed SteelHeads를 사용할 수 있나요?

파트너인 Riverbed가 Azure Site Recovery 작업에 대한 자세한 지침을 제공합니다. [솔루션 가이드](https://community.riverbed.com/s/article/DOC-4627)를 검토하세요.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>ExpressRoute를 사용하여 가상 머신을 Azure로 복제할 수 있습니까?
예, [ExpressRoute를 사용](concepts-expressroute-with-site-recovery.md)하여 온-프레미스 가상 머신을 Azure로 복제할 수 있습니다.

- Azure Site Recovery는 퍼블릭 엔드포인트를 통해 Azure Storage에 데이터를 복제합니다. Site Recovery 복제에 ExpressRoute를 사용하려면 [Microsoft 피어링](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)을 설정하거나, 기존 [퍼블릭 피어링](../expressroute/about-public-peering.md)(새 서킷에서 사용되지 않음)을 사용해야 합니다.
- Microsoft 피어링은 복제에 권장되는 라우팅 도메인입니다.
- 프라이빗 피어링에는 복제가 지원되지 않습니다.
- VMware 머신이나 물리적 머신을 보호하는 경우 구성 서버에 대한 [네트워킹 요구 사항](vmware-azure-configuration-server-requirements.md#network-requirements)도 확인합니다. Site Recovery의 복제 오케스트레이션을 위해 구성 서버에서 특정 URL에 연결해야 합니다. 이 연결에는 ExpressRoute를 사용할 수 없습니다.
- 가상 머신이 Azure Virtual Network에 장애 조치된 후 Azure Virtual Network로 [프라이빗 피어링](../expressroute/expressroute-circuit-peerings.md#privatepeering) 설정을 사용하여 액세스할 수 있습니다.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Azure로 복제할 때 어떤 유형의 스토리지 계정 또는 관리 디스크가 필요합니까?

LRS 또는 GRS 스토리지가 필요합니다. 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 GRS를 사용하는 것이 좋습니다. 계정은 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다. Premium Storage는 Azure Portal에서 Site Recovery를 배포할 때 VMware VM, Hyper-V VM 및 물리적 서버 복제에 지원됩니다. 관리 디스크는 LRS만 지원합니다.

### <a name="how-often-can-i-replicate-data"></a>데이터를 얼마나 자주 복제할 수 있나요?
* **Hyper-V:** Hyper-V VM은 30초(프리미엄 스토리지 제외), 5분 또는 15분마다 복제할 수 있습니다.
* **Azure VM, VMware VM 및 물리적 서버:** 복제 빈도는 이와 관련이 없습니다. 복제가 계속 됩니다.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>기존 복구 사이트에서 3차 사이트로 복제를 확장할 수 있습니까?
확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Azure에 처음으로 복제할 때 오프라인으로 복제할 수 있습니까?
지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-exclude-specific-disks-from-replication"></a>특정 디스크를 복제에서 제외할 수 있습니까?
이는 Azure Portal을 사용하여 Azure에 VMware VM 및 Hyper-V VM을 복제하는 경우에만 지원됩니다.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>동적 디스크를 사용하여 가상 머신을 복제할 수 있습니까?
동적 디스크는 Hyper-V 가상 머신을 복제할 때와, VMware VM 및 물리적 머신을 Azure에 복제할 때 지원됩니다. 운영 체제 디스크는 기본 디스크여야 합니다.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>복제 트래픽에 할당된 대역폭을 스로틀할 수 있나요?
예. 다음 문서에서 대역폭 스로틀에 대해 더 자세히 검토할 수 있습니다.

* [VMware VM 및 물리적 서버를 복제하기 위한 용량 계획](site-recovery-plan-capacity-vmware.md)
* [Azure에 Hyper-V VM을 복제하기 위한 용량 계획](site-recovery-capacity-planning-for-hyper-v-replication.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Linux 서버에서 응용 프로그램 일관성을 사용 하 여 복제를 사용 하도록 설정할 수 있나요? 
예. Linux 운영 체제 Azure Site Recovery는 응용 프로그램 일관성을 위한 응용 프로그램 사용자 지정 스크립트를 지원 합니다. 사전 및 사후 옵션을 포함 하는 사용자 지정 스크립트는 앱 일관성을 유지 하는 동안 Azure Site Recovery 모바일 에이전트에서 사용 됩니다. 이를 사용 하도록 설정 하는 단계는 다음과 같습니다.

1. 컴퓨터에 루트로 로그인 합니다.
2. Azure Site Recovery 모바일 에이전트 설치 위치로 디렉터리를 변경 합니다. 기본값은 "/usr/local/ASR"입니다.<br>
    `# cd /usr/local/ASR`
3. 설치 위치에서 디렉터리를 "VX/scripts"로 변경 합니다.<br>
    `# cd VX/scripts`
4. 루트 사용자에 대 한 execute 권한이 있는 "customscript.sh" 라는 bash 셸 스크립트를 만듭니다.<br>
    a. 스크립트는 "--pre" 및 "--post"를 지원 해야 합니다 (이중 대시 참고) 명령줄 옵션<br>
    b. 사전 옵션을 사용 하 여 스크립트를 호출 하는 경우 응용 프로그램 입/출력을 고정 하 고 사후 옵션을 사용 하 여 호출 하면 응용 프로그램 입/출력을 재개 해야 합니다.<br>
    다. 샘플 템플릿-<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. 앱 일관성을 요구 하는 응용 프로그램에 대 한 사전 및 사후 단계의 입력/출력 고정 및 고정 해제 명령을 추가 합니다. 이러한 스크립트를 지정 하 여 추가 하 고 사전 및 사후 옵션을 사용 하 여 "customscript.sh"에서 호출 하도록 선택할 수 있습니다.

>[!Note]
>사용자 지정 스크립트를 지원 하려면 Site Recovery 에이전트 버전이 9.24 이상 이어야 합니다.

## <a name="failover"></a>장애 조치
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Azure로 장애 조치(failover)하는 경우 장애 조치(failover) 후에 어떻게 Azure VM에 액세스하나요?

보안 인터넷 연결 또는 사이트 간 VPN 또는 Azure ExpressRoute로 Azure VM에 액세스할 수 있습니다. 연결하려면 여러 가지 사항을 준비해야 합니다. [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Azure로 장애 조치(failover)하는 경우 Azure는 어떻게 데이터 복원을 보장합니까?
Azure는 복원을 위해 디자인되었습니다. Site Recovery는 이미 Azure SLA에 따라 보조 Azure 데이터 센터에 장애 조치하도록 설계되었습니다. 이 상황이 발생하면 사용자가 자격 증명 모음에 대해 선택한 지리적으로 동일한 지역에 메타데이터 및 자격 증명 모음이 남아 있습니다.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>두 데이터 센터 간에 복제하는 동안 주 데이터 센터에서 예기치 않게 정전이 발생하면 어떻게 됩니까?
보조 사이트에서 계획되지 않은 장애 조치(failover)를 트리거할 수 있습니다. Site Recovery는 기본 사이트가 연결되지 않아도 장애 조치(failover)를 수행할 수 있습니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?
자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치(failover)를 시작하거나 [Site Recovery PowerShell](/powershell/module/az.recoveryservices) 을 사용하여 장애 조치(failover)를 트리거할 수 있습니다. 장애 복구(failback) 작업은 Site Recovery 포털에서 간단하게 수행할 수 있습니다.

온-프레미스 Orchestrator 또는 Operations Manager를 사용하여 가상 머신 오류를 감지하면 SDK를 사용하여 장애 조치(failover)를 트리거하도록 자동화할 수 있습니다.

* [자세한](site-recovery-create-recovery-plans.md) 복구 계획을 알아봅니다.
* [자세히 알아보기](site-recovery-failover.md) .
* [자세히 알아보기](site-recovery-failback-azure-to-vmware.md)

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>내 온-프레미스 호스트가 응답하지 않거나 손상된 경우 다른 호스트로 장애 복구(failback)할 수 있나요?
예, 대체 위치 복구를 사용하여 Azure에서 다른 호스트로 장애 복구(failback)를 수행할 수 있습니다.

* [VMware 가상 머신](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Hyper-V 가상 머신](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>SDK와 함께 Site Recovery 시나리오를 자동화할 수 있습니까?
예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. 현재 PowerShell을 사용하여 Site Recovery를 배포하기 위한 지원되는 시나리오:

* [VMM 클라우드의 Hyper-V VM을 Azure PowerShell Resource Manager로 복제](hyper-v-vmm-powershell-resource-manager.md)
* [Hyper-V VM을 VMM 없이 Azure PowerShell Resource Manager로 복제](hyper-v-azure-powershell-resource-manager.md)
* [PowerShell 리소스 관리자를 사용하여 VMware에서 Azure로 복제](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>구성 요소/공급자 업그레이드

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Site Recovery 업그레이드의 릴리스 정보/업데이트 롤업을 어디서 찾을 수 있나요?

[새 업데이트](site-recovery-whats-new.md)에 대해 알아보고 및 [롤업 정보 받습니다](service-updates-how-to.md).

## <a name="next-steps"></a>다음 단계
* [Site Recovery 개요](site-recovery-overview.md)

