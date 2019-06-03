---
title: Azure Site Recovery를 사용 하 여 Azure 재해 복구는 VMware에 대 한 일반적인 질문 | Microsoft Docs
description: Azure Site Recovery를 사용 하 여 Azure에 온-프레미스 VMware Vm의 재해 복구에 대 한 일반적인 질문에 답변을 받을 수 있습니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 05/30/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 59be8e0585f0bedcafc868ee42f5113509c9c4ef
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417782"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware에 대 한 일반적인 질문에서 Azure로 복제

이 문서에서는 Azure에 온-프레미스 VMware 가상 머신 (Vm)의 재해 복구를 배포할 때 제기 될 수 있습니다 하는 일반적인 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 재해 복구를 위한 하나요?

[관련된 구성 요소에 알아봅니다](vmware-azure-architecture.md) VMware Vm의 재해 복구에서 합니다.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Site Recovery를 사용 하 여 Azure로 VMware Vm을 마이그레이션할 수 있습니다?

예. VMware Vm에 대 한 전체 재해 복구를 설정 하려면 Site Recovery를 사용 하는 것 외에도 온-프레미스 VMware Vm을 Azure로 마이그레이션할 Site Recovery를 사용할 수도 있습니다. 이 시나리오에서는 Azure Storage에 온-프레미스 VMware Vm을 복제 합니다. 그런 다음, 온-프레미스에서 Azure로 장애 조치합니다. 장애 조치(failover) 후 Azure VM에서 앱과 워크로드가 실행되며 사용할 수 있습니다. 프로세스 제외 하 고 Azure에서 장애 복구 없습니다 마이그레이션에서 전체 재해 복구 설정와 비슷합니다.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>내 Azure 계정에 VM을 만들 수 있는 권한이 필요한가요?

구독 관리자인 경우 필요한 복제 권한을 갖고 있습니다. 관리자로 모르는 경우 이러한 작업을 수행할 수 있는 권한이 필요 합니다.

- 리소스에서 Azure VM을 만들고 그룹 및 가상 네트워크 사이트 복구를 구성 하는 경우를 지정할 수 있습니다.
- 선택한 저장소 계정 또는 구성에 따라 관리 되는 디스크에 작성 합니다.

[자세한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) 필요한 사용 권한에 대 한 합니다.

### <a name="what-applications-can-i-replicate"></a>어떤 애플리케이션을 복제할 수 있나요?

앱 또는 충족 하는 VMware VM에서 실행 중인 워크 로드를 복제할 수 있습니다 합니다 [복제 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)합니다.

- Site Recovery는 앱의 장애 조치 하 고을 지능형 상태로 장애 수 있도록 응용 프로그램 인식 복제를 지원 합니다.
- Site Recovery는 SharePoint, Exchange, Dynamics, SQL Server 및 Active Directory 같은 Microsoft 응용 프로그램과 통합 됩니다. 것도 긴밀히 협력 하 여, Oracle, SAP, IBM 및 Red Hat을 비롯 한 선두 공급 업체입니다.

[자세히 알아봅니다](site-recovery-workload.md) .

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Azure 게스트 OS 서버 라이선스를 사용할 수 있습니까?

예, Microsoft Software Assurance 고객은 사용할 수 있습니다 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) 라이선스는 Azure로 마이그레이션되는 Windows Server 컴퓨터에 대 한 비용을 절약 하거나 재해 복구를 위해 Azure를 사용 하 합니다.

## <a name="security"></a>보안

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Site Recovery에서 VMware 서버에 액세스를 필요 합니까?

Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- Site Recovery 구성 서버를 실행 하는 VMware VM을 설정 합니다.
- 복제할 VM을 자동으로 검색합니다.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Site Recovery는 VMware Vm에 대 한 어떤 액세스를 필요 합니까?

- 로 복제 하려면 VMware VM에 Site Recovery 모바일 서비스를 설치 및 실행 있어야 합니다. 이 도구를 수동으로 배포할 수 있습니다 또는 VM의 복제를 사용 하도록 설정 하면 Site Recovery 서비스의 푸시 설치를 수행을 지정할 수 있습니다.
- 복제 중에 VM은 Site Recovery와 다음과 같이 통신합니다.
    - Vm 복제 관리를 위한 HTTPS 포트 443에서 구성 서버와 통신합니다.
    - Vm은 HTTPS 포트 9443에서 프로세스 서버로 복제 데이터를 전송합니다. (이 설정은 수정할 수 있습니다.)
    - 다중 VM 일관성을 사용하도록 설정하면 VM에서 20004 포트를 통해 서로 통신합니다.

### <a name="is-replication-data-sent-to-site-recovery"></a>복제 데이터를 Site Recovery로 보내는가요?

아니요, Site Recovery는 복제 된 데이터를 가로채지 하 고 Vm에서 실행 되는 대상에 대 한 정보를 갖지 않습니다. 복제 데이터는 Azure Storage 및 VMware 하이퍼바이저 간에 교환 됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery는 ISO 27001:2013, 27018, HIPAA, DPA에 인증 됩니다. 이 SOC2 및 FedRAMP JAB 평가 중입니다.

## <a name="pricing"></a>가격

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>VMware 재해 복구에 대 한 대략적인 요금은 어떻게 계산 하나요?

사용 된 [가격 책정 계산기](https://aka.ms/asr_pricing_calculator) Site Recovery를 사용 하는 동안 비용을 예측 합니다.

비용의 자세한 예상치를 위한 deployment planner 도구 실행 [VMware](https://aka.ms/siterecovery_deployment_planner) 사용 하는 [비용 예측 보고서](https://aka.ms/asr_DP_costreport)합니다.

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>모든 비용 차이 storage 또는 managed disks로 직접 복제?

저장소 계정에서 관리 되는 디스크와 약간 다르게 요금이 청구 됩니다. [자세한](https://azure.microsoft.com/pricing/details/managed-disks/) 관리 디스크 가격 책정에 대 한 합니다.

## <a name="mobility-service"></a>모바일 서비스

### <a name="where-can-i-find-the-mobility-service-installers"></a>모바일 서비스 설치 관리자는 어디서 찾을 수 있나요?

설치 관리자는 구성 서버의 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 폴더에 있습니다.

## <a name="how-do-i-install-the-mobility-service"></a>모바일 서비스를 설치하려면 어떻게 할까요?

복제 하려는 각 VM에 여러 가지 방법 중 하나에서 서비스를 설치 합니다.

- [강제 설치](vmware-physical-mobility-service-overview.md#push-installation)
- [수동 설치](vmware-physical-mobility-service-overview.md#install-mobility-agent-through-ui) UI 또는 PowerShell에서
- 같은 배포 도구를 사용 하 여 배포 [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>관리 디스크

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery에 대 한 데이터를 복제 하는 위치

Site Recovery는 Azure의 managed disks로 온-프레미스 VMware Vm 및 물리적 서버를 복제 합니다.

- Site Recovery 프로세스 서버는 대상 지역의 캐시 저장소 계정에 복제 로그를 기록합니다.
- 이러한 로그의 접두사를 가진 Azure 관리 디스크에서 복구 지점을 만드는 데 사용 됩니다 **asrseeddisk**합니다.
- 장애 조치가 발생 하는 경우 새 대상 관리 되는 디스크를 만들려면 선택한 복구 지점이 사용 됩니다. 이 관리 디스크는 Azure에서 VM에 연결 됩니다.
- (이전 2019 년 3 월) 이전에 저장소 계정에 복제 된 Vm 영향을 받지 않습니다.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>저장소 계정에 새 컴퓨터를 복제할 수 있나요?

아니요. Azure portal에서 3 월 2019부터로 복제할 수 있습니다만 Azure 디스크를 관리 합니다.

저장소 계정에 새 Vm의 복제는 PowerShell 또는 REST API (버전 2018-01-10 또는 2016-08-10)를 사용 하 여만 사용할 수 있습니다.

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Managed disks로 복제 하는 이점은 무엇 인가요?

[에 대해 알아봅니다 어떻게](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) Site Recovery 관리 디스크를 사용 하 여 재해 복구를 간소화 합니다.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>변경 관리 디스크 유형을 컴퓨터가 보호 된 후

예, 쉽게 수행할 수 있습니다 [관리 되는 디스크의 형식을 변경](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) 진행 중인 복제에 대 한 합니다. 종류를 변경 하기 전에 다음 사항을 확인 URL은 관리 디스크에서 생성 된 공유 액세스 서명이 없습니다

1. 로 이동 합니다 **Managed Disk** Azure portal에서 리소스 공유 액세스 서명 URL 배너가 해야 할지 여부를 확인 하 고는 **개요** 블레이드입니다.
1. 배너가 있는 경우 진행 중인 내보내기를 취소 하 고 선택 합니다.
1. 다음 몇 분 내에 디스크의 형식을 변경 합니다. 관리 디스크 유형을 변경 하는 경우 최신 복구 지점을 Azure Site Recovery에 의해 생성 될 때까지 기다립니다.
1. 나중에 테스트 장애 조치 또는 장애 조치에 대해 새 복구 지점을 사용 합니다.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>복제 관리 디스크에서 관리 되지 않는 디스크를 전환할 수 있나요?

아니요. 에 관리 되지 않는 관리 되는 전환에서 지원 되지 않습니다.

## <a name="replication"></a>복제

### <a name="what-are-the-replicated-vm-requirements"></a>복제된 VM에 대한 요구 사항은 무엇인가요?

[자세한](vmware-physical-azure-support-matrix.md#replicated-machines) VMware Vm 및 물리적 서버에 대 한 지원 요구 사항에 대 한 합니다.

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

VMware VM을 Azure에 복제하는 경우에는 복제가 계속됩니다.

### <a name="can-i-extend-replication"></a>복제를 확장할 수 있나요?

확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="can-i-do-an-offline-initial-replication"></a>오프라인 초기 복제를 수행할 수 있나요?

오프 라인 복제는 지원 되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="what-is-asrseeddisk"></a>Asrseeddisk 란?

모든 원본 디스크에 대 한 데이터는 Azure에서 관리 디스크에 복제 됩니다. 이 디스크에 접두사 **asrseeddisk**합니다. 원본 디스크 및 모든 복구 지점 스냅숏의 복사본을 저장합니다.

### <a name="can-i-exclude-disks-from-replication"></a>복제에서 디스크 제외

예, 디스크를 제외할 수 있습니다.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>동적 디스크가 있는 Vm을 복제할 수 있습니까?

예, 동적 디스크는 복제할 수 있습니다. 운영 체제 디스크는 기본 디스크여야 합니다.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>다중 VM 일관성에 대해 복제 그룹을 사용하는 경우 새 VM을 기존 복제 그룹에 추가할 수 있나요?

예, 복제를 활성화하면 새 VM을 기존 복제 그룹에 추가할 수 있습니다. 그러나:

- 복제 시작 된 후 기존 복제 그룹에 VM을 추가할 수 없습니다.
- 기존 Vm에 대 한 복제 그룹을 만들 수 없습니다.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>디스크를 추가하거나 크기를 조정하여 복제 중인 VM을 수정할 수 있나요?

Azure에 VMware 복제에 대 한 디스크 크기를 수정할 수 있습니다. 새 디스크를 추가 하려는 경우 디스크를 추가 하 고 VM에 대 한 보호를 다시 사용 하도록 설정 해야 합니다.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>마이그레이션할 수 온-프레미스 컴퓨터를 새 vCenter Server에 진행 중인 복제에 영향을 주지 않고?

아니요. VMware Vcenter 또는 마이그레이션 변경이 진행 중인 복제에 영향을 줍니다. 새 vCenter Server 사용 하 여 Site Recovery를 설정 하 고 컴퓨터에 대 한 복제를 다시 사용 하도록 설정 합니다.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>가상 네트워크 (Azure 방화벽)에 구성 된 캐시 또는 대상 저장소 계정에 복제할 수 있습니까?

아니요, Site Recovery는 가상 네트워크에서 Azure Storage에 복제를 지원 하지 않습니다.

## <a name="component-upgrade"></a>구성 요소 업그레이드

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>모바일 서비스 에이전트 또는 구성 서버 내 버전이 오래 하 고 업그레이드가 실패 했습니다. 어떻게 해야 합니까?

Site Recovery는 N-4 지원 모델을 따릅니다. [자세한](https://aka.ms/asr_support_statement) 아주 오래 된 버전에서 업그레이드 하는 방법에 대 한 합니다.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>릴리스 정보 및 Azure Site Recovery에 대 한 업데이트 롤업 수 여기서 있나요?

[새 업데이트에 알아봅니다](site-recovery-whats-new.md), 및 [롤업 정보를 가져올](service-updates-how-to.md)합니다.

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Azure로 재해 복구에 대 한 업그레이드 정보는 어디서 찾을 수 있습니까?

[업그레이드 하는 방법에 대해 알아봅니다](https://aka.ms/asr_vmware_upgrades)합니다.

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>각 업그레이드에 대 한 원본 컴퓨터를 다시 부팅 해야 하나요?

부팅이 권장 되지만 각 업그레이드에 대 한 필수 사항은 아닙니다. [자세히 알아보기](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>구성 서버

### <a name="what-does-the-configuration-server-do"></a>구성 서버에서 수행하는 작업은 무엇인가요?

구성 서버는 다음을 포함하여 온-프레미스 Site Recovery 구성 요소를 실행합니다.

- 구성 서버 자체입니다. 서버는 온-프레미스 구성 요소와 Azure 간의 통신을 조정 하 고 데이터 복제를 관리 합니다.
- 프로세스 서버-복제 게이트웨이의 역할을 합니다. 이 서버:
    1. 복제 데이터를 받습니다.
    2. 캐싱, 압축 및 암호화를 사용하여 데이터를 최적화합니다.
    3. Azure Storage에 데이터를 보냅니다.
  프로세스 서버 Vm에 모바일 서비스의 푸시 설치를도 않으며 온-프레미스 VMware Vm의 자동 검색을 수행 합니다.
- 마스터 대상 서버-Azure에서 장애 복구 중 복제 데이터를 처리 합니다.

구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

### <a name="where-do-i-set-up-the-configuration-server"></a>구성 서버를 설정해야 하는 위치는 어떻게 되나요?

구성 서버에 대 한 단일 고가용성 온-프레미스 VMware VM을 필요합니다. 물리적 서버 재해 복구를 위해 물리적 컴퓨터에서 구성 서버를 설치 합니다.

### <a name="what-do-i-need-for-the-configuration-server"></a>구성 서버에 대 한 하나요?

[필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)를 검토하세요.

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>구성 서버는 템플릿을 사용하는 대신 수동으로 설정할 수 있나요?

좋습니다 있습니다 [구성 서버 VM을 만들려면](vmware-azure-deploy-configuration-server.md) 가상 머신 형식 OVF (Open) 템플릿의 최신 버전을 사용 하 여 합니다. 서식 파일 (예: VMware 서버에 액세스할 수 없는 경우)를 사용할 수 없으면 [다운로드](physical-azure-set-up-source.md) 설치 파일을 포털에서 구성 서버를 설정 합니다.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>구성 서버에서 둘 이상의 지역에 복제할 수 있나요?

아니요. 둘 이상의 지역에 복제 하려면 각 지역에서 구성 서버가 필요 합니다.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure에서 구성 서버를 호스팅할 수 있나요?

가능한 경우에 구성 서버를 실행 하는 Azure VM에 온-프레미스 VMware 인프라 및 Vm과 통신 해야 합니다. 이 통신 대기 시간을 추가 하 고 진행 중인 복제에 영향을 줍니다.

### <a name="how-do-i-update-the-configuration-server"></a>구성 서버를 업데이트하려면 어떻게 할까요?

[에 대해 알아봅니다](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) 구성 서버를 업데이트 하는 방법입니다.

- 최신 업데이트 정보를 찾을 수 있습니다 합니다 [Azure 업데이트 페이지](https://azure.microsoft.com/updates/?product=site-recovery)합니다.
- 포털에서 최신 버전을 다운로드할 수 있습니다. 구성 서버에서 직접 최신 버전을 다운로드할 수 있습니다 또는 합니다 [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)합니다.
- 현재 버전 보다 오래 된 4 개 버전 버전을 사용 하는 경우 참조를 [문을 지원](https://aka.ms/asr_support_statement) 업그레이드 지침에 대 한 합니다.

### <a name="should-i-back-up-the-configuration-server"></a>구성 서버를 백업 해야 하나요?

구성 서버의 예약된 정기 백업을 수행하는 것이 좋습니다.

- 성공적으로 장애 복구 되 장애 복구 VM 구성 서버 데이터베이스에 존재 해야 합니다.
- 구성 서버는 실행 및 연결 된 상태에서 여야 합니다.
- [자세한](vmware-azure-manage-configuration-server.md) 일반적인 구성 서버 관리 작업에 대 한 합니다.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>구성 서버를 설정할 때 MySQL을 수동으로 다운로드하여 설치할 수 있나요?

예. MySQL을 다운로드 하 고 C:\Temp\ASRSetup 폴더에 넣습니다. 수동으로 설치 합니다. 구성 서버 VM을 설정하고 조건에 동의하면 MySQL이 **다운로드 및 설치**에 **이미 설치됨**으로 표시됩니다.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>MySQL을 다운로드하지 않고 Site Recovery에서 설치할 수 있나요?

예. MySQL installer를 다운로드 하 고 C:\Temp\ASRSetup 폴더에 넣습니다. 구성 서버 VM 설정할 때 동의 하 고 선택 **다운로드 및 설치**합니다. 포털에는 MySQL을 설치 하 고 추가한 설치 관리자를 사용 합니다.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>다른 용도로 구성 서버 VM을 사용할 수 있나요?

아니요. 구성 서버에만 VM을 사용 합니다.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>구성 서버를 복제하고 오케스트레이션에 사용할 수 있나요?

아니요. 등록 문제를 방지 하려면 새 구성 서버를 설정 합니다.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>구성 서버 등록 된 자격 증명 모음을 변경할 수 있나요?

아니요. 자격 증명 모음을 구성 서버에 연결 된 후에 변경할 수 없습니다. [에 대해 알아봅니다](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) 다른 자격 증명 모음을 사용 하 여 구성 서버를 등록 하는 방법에 대 한 합니다.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>VMware Vm 및 물리적 서버 재해 복구에 대 한 동일한 구성 서버를 사용할 수 있습니까?

예, 하지만 해당 물리적 컴퓨터를 장애 될 수 있는 VMware VM만 다시 합니다.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>구성 서버의 암호는 어디서 다운로드할 수 있나요?

[에 대해 알아봅니다](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) 암호를 다운로드 하는 방법입니다.

### <a name="where-can-i-download-vault-registration-keys"></a>자격 증명 모음 등록 키는 어디서 다운로드할 수 있나요?

Recovery Services 자격 증명 모음에서 선택 **구성 서버** 에 **Site Recovery 인프라** > **관리**합니다. 그런 다음, **서버**를 선택 **등록 키 다운로드** 자격 증명 모음 자격 증명 파일을 다운로드 합니다.

## <a name="process-server"></a>프로세스 서버

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>복제를 활성화 하면 프로세스 서버를 선택할 수 없는 이유는 무엇입니까?

9.24 버전과 이후 표시에서 업데이트를 [복제를 사용 하도록 설정 하면 프로세스 서버의 상태](vmware-azure-enable-replication.md#enable-replication)합니다. 이 기능을 통해 프로세스 서버 제한을 방지 하는 데 비정상 프로세스 서버의 사용을 최소화 합니다.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>버전 9.24 또는 정확 하 게 상태 정보에 대 한 나중에 프로세스 서버를 업데이트 하는 방법

부터는 [9.24 버전](service-updates-how-to.md#links-to-currently-supported-update-rollups), 프로세스 서버의 상태를 나타내기 위해 더 많은 경고가 추가 되었습니다. [9.24 이상 버전에 Site Recovery 구성 요소를 업데이트] (service-updates-how-to.md#links-to-currently-supported-update-rollups) 모든 경고가 생성 되도록 합니다.

## <a name="failover-and-failback"></a>장애 조치 및 장애 복구

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>장애 복구를 위해 온-프레미스 프로세스 서버를 사용할 수 있습니까?

데이터 전송 대기 시간을 방지 하기 위해 장애 복구를 위해 Azure에서 프로세스 서버를 만드는 것이 좋습니다. 또한 구성 서버에 Azure 공용 네트워크를 사용 하 여 원본 Vm 네트워크를 구분 하는 경우 반드시 Azure에서 장애 복구를 위해 만든 프로세스 서버를 사용 합니다.

### <a name="can-i-keep-the-ip-address-on-failover"></a>장애 조치 시 IP 주소 유지

예, 장애 조치 시 IP 주소를 유지할 수 있습니다. 대상 IP 주소를 지정 해야 합니다 **Compute 및 네트워크** 장애 조치 하기 전에 VM에 대 한 설정입니다. 또한 장애 복구 하는 동안 IP 주소 충돌을 방지 하려면 장애 조치 시 컴퓨터를 종료 합니다.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>대상 VM 크기 또는 장애 조치 하기 전에 VM 유형을 변경할 수 있나요?

예, 장애 조치 전에 언제 든 지 형식 또는 VM의 크기를 변경할 수 있습니다. 포털에서 사용 하 여 합니다 **Compute 및 네트워크** 복제 된 VM에 대 한 설정입니다.

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?

Vmware에서 Azure로, 가장 오래 된 복구 지점을 사용할 수는 72 시간입니다.

### <a name="how-do-i-access-azure-vms-after-failover"></a>장애 조치 후 Azure VM에 액세스하려면 어떻게 할까요?

장애 조치 후 Azure Vm 보안 인터넷 연결을 통해, 사이트 간 VPN을 통해 또는 Azure ExpressRoute를 통해 액세스할 수 있습니다. 에 연결 하려면 몇 가지 준비 해야 합니다. [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>장애 조치 데이터 복원 력 있는?

Azure는 복원을 위해 디자인되었습니다. Site Recovery는 Azure 서비스 수준 계약 (SLA)에서 필요에 따라 보조 Azure 데이터 센터에 장애 조치를 위해 설계 되었습니다. 장애 조치가 발생 해야 메타 데이터 및 자격 증명 모음 자격 증명 모음에 대해 선택한 동일한 지역에 유지 합니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

[장애 조치](site-recovery-failover.md)는 자동이 아닙니다. 포털의 단일 선택 하 여 장애 조치를 시작 하거나 사용할 수 있습니다 [PowerShell](/powershell/module/az.recoveryservices) 장애 조치를 트리거할 수 있습니다.

### <a name="can-i-fail-back-to-a-different-location"></a>다른 위치로 장애 조치할 수 있나요?

예. Azure로 장애 조치 하는 경우 원래 사용할 수 없는 경우 다른 위치로 장애 수 있습니다. [자세히 알아보기](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>장애 복구 하려면 개인 피어 링이 있는 VPN 또는 ExpressRoute가 필요한 수행는 이유

Azure에서 장애 복구를 Azure에서 데이터에 온-프레미스 VM에 다시 복사 되 고 개인 액세스가 필요 합니다.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>장애 조치(failover) 후 Azure VM의 크기를 조정할 수 있나요?

아니요, 크기 또는 대상 VM 유형의 장애 조치 후에 변경할 수 없습니다.

## <a name="automation-and-scripting"></a>자동화 및 스크립팅

### <a name="can-i-set-up-replication-with-scripting"></a>스크립팅을 사용하여 복제를 설정할 수 있나요?

예. Rest API, PowerShell 또는 Azure SDK를 사용 하 여 Site Recovery 워크플로 자동화할 수 있습니다. [자세히 알아보기](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>성능 및 용량

### <a name="can-i-throttle-replication-bandwidth"></a>복제 대역폭을 제한할 수 있나요?

예. [자세히 알아보기](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>다음 단계

- 지원 요구 사항을 [검토](vmware-physical-azure-support-matrix.md)합니다.
- VMware에서 Azure로의 복제를 [설정](vmware-azure-tutorial.md)합니다.
