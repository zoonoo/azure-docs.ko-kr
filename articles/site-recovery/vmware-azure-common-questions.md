---
title: 일반적인 질문 - Azure Site Recovery를 사용하여 VMware에서 Azure로 재해 복구 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로의 온-프레미스 VMware VM 재해 복구를 설정할 때 발생하는 일반적인 질문을 요약하고 있습니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 12/31/2018
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 90f3a4571e485e52a47eda34eacf6367aef35933
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320993"
---
# <a name="common-questions---vmware-to-azure-replication"></a>일반적인 질문 - VMware에서 Azure로 복제

이 문서에서는 온-프레미스 VMware VM의 재해 복구를 Azure로 배포할 때 표시되는 일반적인 질문에 대한 대답을 제공합니다. 이 문서를 읽은 후 질문이 있다면 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 게시해 주세요.


## <a name="general"></a>일반
### <a name="how-is-site-recovery-priced"></a>Site Recovery 비용은 어떻게 책정하는가요?
[Azure Site Recovery 가격](https://azure.microsoft.com/pricing/details/site-recovery/) 세부 정보를 검토하세요.

### <a name="how-do-i-pay-for-azure-vms"></a>Azure VM에 대한 요금을 지불하려면 어떻게 할까요?
복제 중에 데이터는 Azure 저장소로 복제되며 어떠한 VM 변경에도 지불하지 않습니다. Azure에 장애 조치를 실행하면 Site Recovery에서 Azure IaaS 가상 머신을 자동으로 만듭니다. 그러면 Azure에서 사용하는 컴퓨팅 리소스에 대한 요금이 청구됩니다.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Azure로 복제할 때 VMware를 사용하여 수행할 수 있는 작업은 무엇인가요?
- **재해 복구**: 전체 재해 복구를 설정할 수 있습니다. 이 시나리오에서는 온-프레미스 VMware VM을 Azure 저장소에 복제합니다. 그런 다음, 온-프레미스 인프라를 사용할 수 없는 경우 Azure로 장애 조치할 수 있습니다. 장애 조치를 수행하면 복제된 데이터를 사용하여 Azure VM이 만들어집니다. 온-프레미스 데이터 센터를 다시 사용할 수 있을 때까지 Azure VM에서 응용 프로그램 및 작업에 액세스할 수 있습니다. 그런 다음, Azure에서 온-프레미스 사이트로 장애 복구할 수 있습니다.
- **마이그레이션**: Site Recovery를 사용하여 온-프레미스 VMware VM을 Azure로 마이그레이션할 수 있습니다. 이 시나리오에서는 온-프레미스 VMware VM을 Azure 저장소에 복제합니다. 그런 다음, 온-프레미스에서 Azure로 장애 조치합니다. 장애 조치(failover) 후 Azure VM에서 앱과 워크로드가 실행되며 사용할 수 있습니다.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Azure에는 무엇이 필요한가요?
Azure 구독, Recovery Services 저장소, 저장소 계정 및 가상 네트워크가 필요합니다. 자격 증명 모음, 저장소 계정 및 네트워크는 동일한 지역에 있어야 합니다.

### <a name="what-azure-storage-account-do-i-need"></a>필요한 Azure 저장소 계정은 무엇인가요?
LRS 또는 GRS 저장소 계정이 필요합니다. 지역 정전이 발생하거나 주 지역을 복구할 수 없는 경우에 데이터를 복원할 수 있도록 GRS를 사용하는 것이 좋습니다. Premium Storage가 지원됩니다.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>내 Azure 계정에 VM을 만들 수 있는 권한이 필요한가요?
구독 관리자인 경우 필요한 복제 권한을 갖고 있습니다. 구독 관리자가 아닌 경우 Site Recovery를 구성할 때 지정한 리소스 그룹 및 가상 네트워크에 Azure VM을 만들 수 있는 권한과 선택한 저장소 계정에 쓸 수 있는 권한이 필요합니다. [자세히 알아보기](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>온-프레미스

### <a name="what-do-i-need-on-premises"></a>온-프레미스에는 무엇이 필요한가요?

온-프레미스에서 다음이 필요합니다.
- 단일 VMware VM에 설치된 Site Recovery 구성 요소
- 하나 이상의 ESXi 호스트가 있는 VMware 인프라가 필요하며 vCenter 서버를 사용하는 것이 좋습니다.
- 복제할 하나 이상의 VMware VM도 필요합니다.

VMware에서 Azure로 복제 아키텍처에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

다음과 같이 온-프레미스 구성 서버를 배포할 수 있습니다.

- 미리 설치된 구성 서버와 함께 OVA 템플릿을 사용하여 VMware VM으로 구성 서버를 배포하는 것이 좋습니다.
- 어떤 이유로 템플릿으로 사용할 수 없는 경우 구성 서버를 수동으로 설정할 수 있습니다. [자세히 알아보기](physical-azure-disaster-recovery.md#set-up-the-source-environment).



### <a name="where-do-on-premises-vms-replicate-to"></a>온-프레미스 VM은 어디에 복제되는가요?
데이터는 Azure 저장소에 복제됩니다. 장애 조치를 실행하면 Site Recovery에서 저장소 계정으로부터 Azure VM을 자동으로 만듭니다.

## <a name="replication"></a>복제

### <a name="what-apps-can-i-replicate"></a>복제할 수 있는 앱은 무엇인가요?
[복제 요구 사항](vmware-physical-azure-support-matrix.md##replicated-machines)을 준수하는 VMware VM에서 실행되는 모든 응용 프로그램 또는 작업을 복제할 수 있습니다. Site Recovery는 애플리케이션 인식 복제를 지원하므로 앱을 지능형 상태로 장애 조치 및 장애 복구할 수 있습니다. SharePoint, Exchange, Dynamics, SQL Server, Active Directory와 같은 Microsoft 애플리케이션과 통합되고, Oracle, SAP, IBM, Red Hat과 같은 선도적인 공급 업체와 긴밀하게 협력합니다. [자세히 알아봅니다](site-recovery-workload.md) .

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 Azure에 복제할 수 있나요?
Site Recovery는 공용 엔드포인트를 통하거나 ExpressRoute 공용 피어링을 사용하여 온-프레미스에서 Azure 저장소로 데이터를 복제합니다. 사이트 간 VPN 네트워크를 통한 복제는 지원되지 않습니다.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>ExpressRoute를 사용하여 Azure에 복제할 수 있나요?
예, ExpressRoute를 사용하여 VM을 Azure로 복제할 수 있습니다. Site Recovery는 공용 엔드포인트를 통해 Azure Storage 계정에 데이터를 복제합니다. Site Recovery 복제에 ExpressRoute를 사용하려면 [공용 피어링](../expressroute/expressroute-circuit-peerings.md#publicpeering) 또는 [Microsoft 피어링](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)을 설정해야 합니다. Microsoft 피어링은 복제에 권장되는 라우팅 도메인입니다. 복제를 위해 [네트워킹 요구 사항](vmware-azure-configuration-server-requirements.md#network-requirements)이 충족되는지도 확인합니다. VM에서 Azure 가상 네트워크로 장애 조치한 후에는 [개인 피어링](../expressroute/expressroute-circuit-peerings.md#privatepeering)을 사용하여 해당 VM에 액세스할 수 있습니다.


### <a name="why-cant-i-replicate-over-vpn"></a>VPN을 통해 복제할 수 없는 이유는 무엇인가요?

Azure에 복제하는 경우 복제 트래픽이 Azure Storage 계정의 공용 엔드포인트에 도달하므로, ExpressRoute(공용 피어링)를 사용하여 공용 인터넷을 통해서만 복제할 수 있으며 VPN은 작동하지 않습니다.


### <a name="what-are-the-replicated-vm-requirements"></a>복제된 VM에 대한 요구 사항은 무엇인가요?

복제의 경우 VMware VM에서 지원되는 운영 체제를 실행해야 합니다. 또한 VM에서 Azure VM에 대한 요구 사항을 충족해야 합니다. 지원 매트릭스에서 [자세히 알아보세요](vmware-physical-azure-support-matrix.md##replicated-machines).

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?
VMware VM을 Azure에 복제하는 경우에는 복제가 계속됩니다.

### <a name="can-i-extend-replication"></a>복제를 확장할 수 있나요?
확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-initial-replication"></a>오프라인 초기 복제를 수행할 수 있나요?
지원되지 않습니다. [사용자 의견 포럼](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-exclude-disks"></a>디스크를 제외할 수 있나요?
예, 디스크는 복제에서 제외할 수 있습니다.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>동적 디스크를 사용하여 VM을 복제할 수 있나요?
예, 동적 디스크는 복제할 수 있습니다. 운영 체제 디스크는 기본 디스크여야 합니다.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>다중 VM 일관성에 대해 복제 그룹을 사용하는 경우 새 VM을 기존 복제 그룹에 추가할 수 있나요?
예, 복제를 활성화하면 새 VM을 기존 복제 그룹에 추가할 수 있습니다. 복제가 시작된 후 VM을 기존 복제 그룹에 추가할 수 없으며 기존 VM에 대한 복제 그룹을 만들 수 없습니다.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>디스크를 추가하거나 크기를 조정하여 복제 중인 VM을 수정할 수 있나요?

Azure로 VMware 복제의 경우 디스크 크기를 수정할 수 있습니다. 새 디스크를 추가하려는 경우 디스크를 추가하고 VM에 대한 보호를 다시 활성화해야 합니다.

### <a name="can-i-migrate-on-prem-machines-to-a-new-vcenter-without-impacting-ongoing-replication"></a>진행 중인 복제에 영향을 주지 않고 온-프레미스 머신을 새 Vcenter에 마이그레이션할 수 있나요?
아니요, Vcenter를 변경하거나 마이그레이션하면 진행 중인 복제에 영향을 줍니다. 새 Vcenter로 ASR를 설정하고 해당 머신에 대해 복제를 사용하도록 설정해야 합니다.

### <a name="can-i-replicate-to-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>Vnet(Azure Storage 방화벽 포함)이 구성되어 있는 캐시/대상 스토리지 계정에 복제할 수 있나요?
아니요, Azure Site Recovery는 Vnet의 스토리지에 복제하는 기능을 지원하지 않습니다.

## <a name="configuration-server"></a>구성 서버

### <a name="what-does-the-configuration-server-do"></a>구성 서버에서 수행하는 작업은 무엇인가요?
구성 서버는 다음을 포함하여 온-프레미스 Site Recovery 구성 요소를 실행합니다.
- 구성 서버 - 온-프레미스와 Azure 간의 통신을 조정하고 데이터 복제를 관리합니다.
- 프로세스 서버 - 복제 게이트웨이의 역할을 합니다. 즉 복제 데이터를 수신하고, 캐싱, 압축 및 암호화를 통해 최적화하며, Azure 저장소로 보냅니다. 또한 프로세스 서버는 복제하려는 VM에 모바일 서비스를 설치하고, 온-프레미스 VMware VM에 대한 자동 검색을 수행합니다.
- 마스터 대상 서버 - Azure에서 장애 복구 중에 복제 데이터를 처리합니다.

구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

### <a name="where-do-i-set-up-the-configuration-server"></a>구성 서버를 설정해야 하는 위치는 어떻게 되나요?
구성 서버에는 단일 고가용성 온-프레미스 VMware VM이 필요합니다.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>구성 서버에 대한 요구 사항은 무엇인가요?

[필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)를 검토하세요.

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>구성 서버는 템플릿을 사용하는 대신 수동으로 설정할 수 있나요?
최신 버전의 OVF 템플릿을 사용하여 [구성 서버 VM을 만드는](vmware-azure-deploy-configuration-server.md) 것이 좋습니다. VMware 서버에 대한 액세스 권한이 없는 것처럼 어떤 이유로 액세스할 수 없는 경우 포털에서 [통합 설치 파일을 다운로드](physical-azure-set-up-source.md)하여 VM에서 실행할 수 있습니다.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>구성 서버에서 둘 이상의 지역에 복제할 수 있나요?
 아니요. 이렇게 하려면 각 지역마다 구성 서버를 설정해야 합니다.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure에서 구성 서버를 호스팅할 수 있나요?
가능한 경우 구성 서버를 실행하는 Azure VM에서 온-프레미스 VMware 인프라 및 VM과 통신해야 합니다. 이로 인해 대기 시간이 늘어나고 진행 중인 복제에 영향을 줄 수 있습니다.

### <a name="how-do-i-update-the-configuration-server"></a>구성 서버를 업데이트하려면 어떻게 할까요?
구성 서버 업데이트에 [대해 알아봅니다](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server). 최신 업데이트 정보는 [Azure 업데이트 페이지](https://azure.microsoft.com/updates/?product=site-recovery)에서 찾을 수 있습니다. [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 최신 버전의 구성 서버를 직접 다운로드할 수도 있습니다.

### <a name="should-i-backup-the-deployed-configuration-server"></a>배포된 구성 서버를 백업해야 하나요?
구성 서버의 예약된 정기 백업을 수행하는 것이 좋습니다. 성공적인 장애 복구(Failover)를 위해서는 장애 복구(Failover)하려는 가상 머신이 구성 서버 데이터베이스에 있어야 하고 구성 서버가 실행 중이고 연결된 상태여야 합니다. [여기](vmware-azure-manage-configuration-server.md)에서 일반 구성 서버 관리 작업에 대해 자세히 알아볼 수 있습니다.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>구성 서버를 설정할 때 MySQL을 수동으로 다운로드하여 설치할 수 있나요?
예. MySQL을 다운로드하여 **C:\Temp\ASRSetup** 폴더에 넣습니다. 그런 다음, 수동으로 설치합니다. 구성 서버 VM을 설정하고 조건에 동의하면 MySQL이 **다운로드 및 설치**에 **이미 설치됨**으로 표시됩니다.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>MySQL을 다운로드하지 않고 Site Recovery에서 설치할 수 있나요?
예. MySQL 설치 관리자를 다운로드하여 **C:\Temp\ASRSetup** 폴더에 넣습니다.  구성 서버 VM을 설정하고, 조건에 동의하고, **다운로드 및 설치**를 클릭하면 포털에서 추가한 설치 관리자를 사용하여 MySQL을 설치합니다.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>다른 용도로 구성 서버 VM을 사용할 수 있나요?
아니요, 구성 서버로만 VM을 사용해야 합니다. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>구성 서버를 복제하고 오케스트레이션에 사용할 수 있나요?
아니요, 등록 문제를 방지하기 위해 새 구성 서버를 설치해야 합니다.

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>구성 서버에 등록된 자격 증명 모음을 변경할 수 있나요?
 아니요. 구성 서버에 등록된 후에는 자격 증명 모음을 변경할 수 없습니다. 재등록 단계는 [이 문서](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault)를 검토하세요.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>VMware VM 및 실제 서버의 재해 복구에 동일한 구성 서버를 사용할 수 있나요?
예, 하지만 물리적 머신은 VMware VM으로만 장애 복구(failback)할 수 있습니다.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>구성 서버의 암호는 어디서 다운로드할 수 있나요?
[이 문서를 검토](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)하여 암호를 다운로드 하는 방법에 대해 알아봅니다.

### <a name="where-can-i-download-vault-registration-keys"></a>자격 증명 모음 등록 키는 어디서 다운로드할 수 있나요?

**Recovery Services 자격 증명 모음**에서 **관리** > **Site Recovery 인프라** > **구성 서버**를 통해 다운로드합니다. **서버**에서 **등록 키 다운로드**를 선택하여 자격 증명 모음 자격 증명 파일을 다운로드합니다.



## <a name="mobility-service"></a>모바일 서비스

### <a name="where-can-i-find-the-mobility-service-installers"></a>모바일 서비스 설치 관리자는 어디서 찾을 수 있나요?
설치 관리자는 구성 서버의 **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** 폴더에 보관됩니다.

## <a name="how-do-i-install-the-mobility-service"></a>모바일 서비스를 설치하려면 어떻게 할까요?
복제하려는 각 VM에 [푸시 설치](vmware-azure-install-mobility-service.md)를 사용하여 설치하거나, UI 또는 PowerShell을 사용하여 [수동으로 설치](vmware-physical-mobility-service-install-manual.md)합니다. 또는 [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)와 같은 배포 도구를 사용하여 배포할 수 있습니다.



## <a name="security"></a>보안

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Site Recovery에서 VMware 서버에 액세스하는 데 필요한 권한은 무엇인가요?
Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- 구성 서버 및 다른 온-프레미스 Site Recovery 구성 요소를 실행하는 VMware VM을 설정합니다. [자세히 알아보기](vmware-azure-deploy-configuration-server.md)
- 복제할 VM을 자동으로 검색합니다. 자동 검색을 위한 계정을 준비하는 방법을 알아봅니다. [자세히 알아보기](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Site Recovery에서 VMware VM에 액세스하는 데 필요한 권한은 무엇인가요?

- 복제하려면 VMware VM에 Site Recovery 모바일 서비스가 설치되어 실행되고 있어야 합니다. 도구를 수동으로 배포하거나, VM에 대한 복제를 사용하도록 설정할 때 Site Recovery에서 서비스의 푸시 설치를 수행하도록 지정할 수 있습니다. 푸시 설치의 경우 Site Recovery에는 서비스 구성 요소를 설치하는 데 사용할 수 있는 계정이 필요합니다. [자세히 알아보기](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). 프로세스 서버(기본적으로 구성 서버에서 실행됨)에서 이 설치를 수행합니다. 모바일 서비스 설치에 대해 [자세히 알아보세요](vmware-azure-install-mobility-service.md).
- 복제 중에 VM은 Site Recovery와 다음과 같이 통신합니다.
    - VM은 복제 관리를 위해 HTTPS 443 포트에서 구성 서버와 통신합니다.
    - VM은 복제 데이터를 HTTPS 9443 포트(수정 가능)에서 프로세스 서버로 보냅니다.
    - 다중 VM 일관성을 사용하도록 설정하면 VM에서 20004 포트를 통해 서로 통신합니다.


### <a name="is-replication-data-sent-to-site-recovery"></a>복제 데이터를 Site Recovery로 보내는가요?
아니요, Site Recovery는 복제된 데이터를 가로채지 않으며, VM에서 실행되는 항목에 대한 정보를 갖지 않습니다. 복제 데이터는 VMware 하이퍼바이저와 Azure 저장소 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA 인증을 받았으며, SOC2 및 FedRAMP JAB 평가를 수행하는 중입니다.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>지리적 지역 내에서 온-프레미스 메타데이터를 유지할 수 있나요?
예. 한 지역에 자격 증명 모음을 만들 때 Site Recovery에서 사용되는 모든 메타데이터가 해당 지역의 지리적 경계 내에 유지되도록 합니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?
예, 전송 중 암호화 및 [Azure의 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption)가 모두 지원됩니다.


## <a name="failover-and-failback"></a>장애 조치 및 장애 복구
### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?
VMware에서 Azure로 복구하는 경우 사용할 수 있는 가장 오래된 복구 지점은 72시간입니다.

### <a name="how-do-i-access-azure-vms-after-failover"></a>장애 조치 후 Azure VM에 액세스하려면 어떻게 할까요?
장애 조치 후에 보안 인터넷 연결, 사이트 간 VPN 또는 Azure ExpressRoute를 통해 Azure VM에 액세스할 수 있습니다. 연결하려면 여러 가지 사항을 준비해야 합니다. [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>장애 조치된 데이터는 복원되나요?
Azure는 복원을 위해 디자인되었습니다. Site Recovery는 Azure SLA에 따라 보조 Azure 데이터 센터에 장애 조치하도록 설계되었습니다. 장애 조치가 발생하면 메타데이터와 자격 증명 모음이 자격 증명 모음에 대해 선택한 지리적 지역과 동일한 지역에 유지되고 있는지 확인합니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?
[장애 조치](site-recovery-failover.md)는 자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치를 시작하거나 [PowerShell](/powershell/module/azurerm.siterecovery)을 사용하여 장애 조치를 트리거할 수 있습니다.

### <a name="can-i-fail-back-to-a-different-location"></a>다른 위치로 장애 조치할 수 있나요?
예, Azure에 장애 조치한 경우 원본을 사용할 수 없을 때 다른 위치로 장애 복구할 수 있습니다. [자세히 알아보기](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>장애 복구하는 데 VPN 또는 ExpressRoute가 필요한 이유는 무엇인가요?
Azure에서 장애 복구하는 경우 Azure의 데이터가 온-프레미스 VM으로 다시 복사되고 개인 액세스가 필요합니다.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>장애 조치(failover) 후 Azure VM의 크기를 조정할 수 있나요?
아니요, 장애 조치(failover) 후에 대상 VM의 크기를 변경할 수 없습니다.


## <a name="automation-and-scripting"></a>자동화 및 스크립팅

### <a name="can-i-set-up-replication-with-scripting"></a>스크립팅을 사용하여 복제를 설정할 수 있나요?
예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. [자세한 정보](vmware-azure-disaster-recovery-powershell.md)

## <a name="performance-and-capacity"></a>성능 및 용량
### <a name="can-i-throttle-replication-bandwidth"></a>복제 대역폭을 제한할 수 있나요?
예. [자세히 알아보기](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>다음 단계
* 지원 요구 사항을 [검토](vmware-physical-azure-support-matrix.md)합니다.
* VMware에서 Azure로의 복제를 [설정](vmware-azure-tutorial.md)합니다.
