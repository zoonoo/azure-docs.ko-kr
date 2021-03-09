---
title: Azure Backup 용어집
description: 이 문서에서는 Azure Backup와 함께 사용 하는 데 도움이 되는 용어를 정의 합니다.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: fb46415c8bdb463556d57004e37d741c1b9a9b57
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502028"
---
# <a name="azure-backup-glossary"></a>Azure Backup 용어집

이 용어는 Azure Backup 사용 하는 경우 유용할 수 있습니다.

>[!NOTE]
>
> - 접두사 "(워크 로드 관련 용어)"로 표시 되는 용어는 Azure Backup에서 지 원하는 특정 작업 하위 집합의 컨텍스트에서만 관련 된 용어를 나타냅니다.
> - Azure Backup 설명서에서 일반적으로 사용 되지만 다른 Azure 서비스를 참조 하는 용어에 대 한 직접 링크는 관련 Azure 서비스의 설명서에 제공 됩니다.

## <a name="afs-azure-file-shares"></a>AFS (Azure 파일 공유)

[Azure Files 설명서](../storage/files/storage-files-introduction.md)를 참조 하세요.

## <a name="alternate-location-recovery"></a>대체 위치 복구

복구 지점에서 백업을 수행한 원래 위치가 아닌 다른 위치로 복구를 수행 했습니다. Azure VM backup을 사용 하는 경우이는 백업을 수행한 원래 서버가 아닌 다른 서버로 VM을 복원 하는 것을 의미 합니다. Azure 파일 공유 백업을 사용 하는 경우 백업 된 파일 공유와 다른 파일 공유로 데이터를 복원 하는 것을 의미 합니다.

## <a name="application-consistent-backup"></a>응용 프로그램 일치 백업

(워크 로드 관련 용어)

응용 프로그램 일치 백업은 메모리 콘텐츠 및 보류 중인 i/o 작업을 캡처합니다. 앱 일치 스냅숏은 [VSS](#vss-windows-volume-shadow-copy-service) vss writer (또는 Linux 용 사전 또는 사후 스크립트)를 사용 하 여 백업이 발생 하기 전에 앱 데이터의 일관성을 유지 합니다. [자세히 알아보기](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>ARM(Azure Resource Manager) 템플릿

[ARM 템플릿 설명서](../azure-resource-manager/templates/overview.md)를 참조 하세요.

## <a name="autoprotection-for-databases"></a>Autoprotection (데이터베이스용)

(워크 로드 관련 용어)

Autoprotection은 독립 실행형 SQL Server 인스턴스 또는 SQL Server Always On 가용성 그룹의 모든 데이터베이스를 자동으로 보호할 수 있는 기능입니다. 기존 데이터베이스에 대 한 백업을 사용 하도록 설정 하는 것이 아니라 나중에 추가할 수 있는 모든 데이터베이스도 보호 합니다.

## <a name="availability-storage-replication-types"></a>가용성 (저장소 복제 유형)

Azure Backup는 저장소 및 데이터를 항상 사용 가능 하 게 유지 하는 세 가지 유형의 복제를 제공 합니다.

### <a name="lrs"></a>LRS

[LRS (로컬 중복 저장소)](../storage/common/storage-redundancy.md#locally-redundant-storage) 는 데이터 센터의 저장소 배율 단위로 백업 데이터를 세 번 복제 합니다 (백업 데이터의 복사본 3 개 생성). 백업 데이터의 모든 복사본은 동일한 지역 내에 있습니다. LRS는 로컬 하드웨어 오류 로부터 백업 데이터를 보호 하기 위한 저렴 한 옵션입니다.

### <a name="grs"></a>GRS

[GRS(지역 중복 스토리지)](../storage/common/storage-redundancy.md#geo-redundant-storage)는 기본값이며 권장하는 복제 옵션입니다. GRS는 원본 데이터의 기본 위치에서 수백 마일 떨어진 보조 지역에 백업 데이터를 복제 합니다. GRS는 LRS 보다 더 많은 비용이 소요 되지만, GRS는 지역 가동 중단이 발생 하더라도 백업 데이터에 대 한 높은 수준의 영속성을 제공 합니다.

>[!NOTE]
>영역 간 복원 기능이 활성화 된 GRS 자격 증명 모음의 경우 백업 저장소는 GRS에서 RA로 업그레이드 됩니다 (읽기 액세스 Geo-Redundant 저장소).

### <a name="zrs"></a>ZRS

[ZRS (영역 중복 저장소)](../storage/common/storage-redundancy.md#zone-redundant-storage) 는 [가용성 영역](../availability-zones/az-overview.md#availability-zones)에서 백업 데이터를 복제 하 여 동일한 지역에 백업 데이터 상주 및 복원 력을 보장 합니다. 따라서 [데이터 상주](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) 필요한 중요 한 워크 로드를 ZRS로 백업할 수 있습니다.

## <a name="azure-command-line-interface-cli"></a>Azure CLI(명령줄 인터페이스)

[Azure CLI 설명서](/cli/azure/what-is-azure-cli)를 참조 하세요.

## <a name="azure-policy"></a>Azure Policy

[Azure Policy 설명서](../governance/policy/overview.md)를 참조 하세요.

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell 설명서](/powershell/azure/)를 참조 하세요.

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

[Azure Resource Manager 설명서](../azure-resource-manager/management/overview.md)를 참조 하세요.

## <a name="azure-disk-encryption-ade"></a>ADE(Azure Disk Encryption)

[Azure Disk Encryption 설명서](../security/fundamentals/azure-disk-encryption-vms-vmss.md)를 참조 하세요.

## <a name="backend-storage--cloud-storage--backup-storage"></a>백 엔드 저장소/클라우드 저장소/백업 저장소

백업 인스턴스에서 사용 되는 실제 저장소입니다. 백업 및 보존 정책에 정의 된 대로 백업 인스턴스에 대해 존재 하는 모든 보존 지점의 크기를 포함 합니다.

## <a name="bare-metal-backup"></a>완전 백업

운영 체제 파일 백업 및 중요 한 볼륨의 모든 데이터 (사용자 데이터 제외) 정의에 따라 완전 백업에는 시스템 상태 백업이 포함 됩니다. BMR 백업은 컴퓨터가 시작되지 않고 모든 것을 복구해야 할 경우 보호 기능을 제공합니다. [자세히 알아보기](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>백업 확장/v m 확장

(Azure VM 워크 로드 유형에만 해당)

Azure VM(가상 머신) 확장은 Azure VM에서 배포 후 구성 및 자동화 태스크를 제공하는 작은 애플리케이션입니다. Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. 이러한 확장을 자동으로 관리 하 Azure Backup 사용자는 이러한 확장을 수동으로 업데이트할 필요가 없습니다.

## <a name="backup-instance--backup-item"></a>백업 인스턴스/백업 항목

특정 백업 및 보존 정책을 사용 하 여 자격 증명 모음에 백업 된 데이터 원본은 백업 인스턴스나 백업 항목을 구성 합니다.

## <a name="backup-rule--backup-policy"></a>백업 규칙/백업 정책

백업 규칙은 데이터 원본에서 백업을 수행 하는 시기 및 빈도를 지정 하는 사용자 정의 규칙입니다. 일부 워크 로드 유형에 대해 백업 정책은 데이터 원본 (전체, 증분, 차등)에 적용할 스냅숏 메서드를 지정 하는 방법도 제공 합니다. 백업 정책은 백업 규칙 및 보존 규칙의 조합으로 생성 되는 경우가 많습니다.

## <a name="backup-vault"></a>Backup 자격 증명 모음

*Microsoft DataProtection/backupvaults* 형식의 Azure Resource Manager 리소스입니다. 현재 백업 자격 증명 모음을 사용 하 여 PostgreSQL 서버용 Azure 데이터베이스를 백업 합니다. [백업 자격 증명 모음에 대해 자세히 알아보세요](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (비즈니스 연속성 및 재해 복구)

BCDR는 비즈니스 중단을 최소화 하면서 계획 되거나 계획 되지 않은 서비스 또는 Azure 중단 중에 앱과 워크 로드를 실행 하 고 실행 하도록 하기 위해 조직에서 채택 해야 하는 일련의 프로세스를 포함 합니다. Azure에서 제공 하는 다양 한 서비스에 [대해 자세히 알아보고](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) 소리 BCDR 전략을 만드는 데 도움을 줍니다.

## <a name="churn"></a>Churn

연속 된 두 백업 간에 백업 되는 데이터의 변경 비율입니다. 새 데이터를 추가 하거나 기존 데이터를 수정 또는 삭제 했기 때문일 수 있습니다.

## <a name="crash-consistent-backup"></a>크래시 일치 백업

(워크 로드 관련 용어)

크래시 일관성 스냅숏은 일반적으로 백업 시 Azure VM이 종료 되는 경우에 발생 합니다. 백업 시 디스크에 이미 존재하는 데이터만 캡처 및 백업됩니다. [자세히 알아보기](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>CRR (영역 간 복원)

[복원 옵션](backup-azure-arm-restore-vms.md#restore-options)중 하나로, crr (교차 지역 복원)을 사용 하면 [Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md#what-are-paired-regions)인 보조 지역에서 백업 항목을 복원할 수 있습니다.

## <a name="data-box"></a>Data box

[Data box 설명서](../databox/data-box-overview.md)를 참조 하세요.

## <a name="datasource"></a>데이터 원본

백업 후보로 사용할 리소스 (Azure 리소스, 프록시 리소스 또는 온-프레미스 리소스)입니다. 예를 들어 Azure VM 또는 Azure 파일 공유입니다.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(워크 로드 관련 용어)

[DPM 설명서](/system-center/dpm/dpm-overview)를 참조 하십시오.

## <a name="expressroute"></a>ExpressRoute

[Express 경로 설명서](../expressroute/expressroute-introduction.md)를 참조 하세요.

## <a name="file-system-consistent-backup"></a>파일 시스템 일치 백업

(워크 로드 관련 용어)

파일 시스템 일치 백업은 모든 파일의 스냅숏을 동시에 가져와서 일관성을 제공 합니다. [자세히 알아보기](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>프런트 엔드 저장소/원본 크기

데이터 원본에 대해 백업할 데이터의 크기입니다. Datasource의 프런트 엔드 크기는 [보호 된 인스턴스](#protected-instance) 수를 결정 합니다.

## <a name="full-backup"></a>전체 백업

전체 백업에서 전체 데이터 원본의 복사본은 모든 백업에 대해 저장 됩니다.

## <a name="gfs-backup-policy"></a>GFS 백업 정책

GFS (조부) 백업 정책은 매일 백업 일정 외에 매주, 매월 및 매년 백업 일정을 정의할 수 있는 정책입니다. 주간 백업은 "sons"로 알려져 있고 월별 백업은 "fathers"로 알려져 있으며 연도별 백업을 "grandfathers" 이라고 합니다. 이러한 각 백업 복사본 집합은 다양 한 기간 동안 보존 되도록 구성할 수 있으므로 백업 복사본에 대 한 보존 선택 항목을 더 자세히 사용자 지정할 수 있습니다. GFS 정책은 저장소 효율이 더 효율적인 방식으로 오랜 시간 동안 백업을 유지 하는 데 유용 합니다.

## <a name="iaas-vms--azure-vms"></a>IaaS Vm/Azure Vm

[AZURE VM 설명서](../virtual-machines/index.yml)를 참조 하세요.

## <a name="incremental-backup"></a>증분 백업

증분 백업은 이전 백업 이후에 변경 된 블록만 저장 합니다.

## <a name="instant-restore"></a>즉시 복원

(작업 관련 용어) 즉시 복원에는 자격 증명 모음에 있는 스냅숏의 복사본이 아니라 백업 스냅숏에서 직접 컴퓨터를 복원 하는 작업이 포함 됩니다. 즉시 복원은 자격 증명 모음에서 복원 하는 것 보다 빠릅니다. 사용 가능한 즉시 복원 지점의 수는 스냅숏에 대해 구성 된 보존 기간에 따라 달라 집니다. 현재 Azure VM 백업에만 적용 됩니다.

## <a name="iops"></a>IOPS

초당 입력/출력 작업 수입니다.

## <a name="item-level-restore"></a>항목 수준 복원

(워크 로드 관련 용어)

복구 지점에서 컴퓨터 내의 개별 파일 또는 폴더를 복원 하는 중입니다.

## <a name="job"></a>작업

사용자 또는 Azure Backup 서비스에서 만든 백업 관련 작업입니다. 작업은 예약 된 또는 주문형 (임시) 일 수 있습니다. 백업, 복원, 보호 구성 등의 여러 가지 작업 유형이 있습니다. [작업에 대해 자세히 알아보세요](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

## <a name="mabs--azure-backup-server"></a>MABS/Azure Backup Server

(워크 로드 관련 용어)

Azure Backup Server로 Hyper-V VM, Microsoft SQL Server, SharePoint Server, 단일 콘솔의 Microsoft Exchange 및 Windows 클라이언트와 같은 애플리케이션 워크로드를 보호할 수 있습니다. DPM에서 많은 워크 로드 백업 기능을 상속 하지만 몇 가지 차이점이 있습니다. [자세히 알아보기](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>관리 디스크

[관리 디스크 설명서](../virtual-machines/managed-disks-overview.md)를 참조 하세요.

## <a name="mars-agent"></a>MARS 에이전트

(워크 로드 관련 용어)

**Azure Backup 에이전트** 또는 **Recovery Services 에이전트가** 라고도 하는 MARS 에이전트는 Azure Backup에서 온-프레미스 컴퓨터 및 azure Vm의 데이터를 azure의 백업 Recovery Services 자격 증명 모음으로 백업 하는 데 사용 됩니다. [자세히 알아보기](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>NSG (네트워크 보안 그룹)

[Nsg 설명서](../virtual-network/network-security-groups-overview.md)를 참조 하세요.

## <a name="offline-seeding"></a>오프 라인 시드

오프 라인 시드는 네트워크 대역폭을 사용 하지 않고 초기 (전체) 백업을 오프 라인으로 전송 하는 프로세스를 말합니다. 백업 데이터를 실제 저장 장치에 복사 하는 메커니즘을 제공 합니다 .이 장치는 가까운 Azure 데이터 센터에 제공 되어 Recovery Services 자격 증명 모음에 업로드 됩니다. [자세히 알아보기](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>주문형 백업/임시 백업

리소스에 대해 구성 된 백업 일정 (정책)을 기반으로 하지 않고 사용자가 일회성으로 요구 하는 백업 작업입니다.

## <a name="original-location-recovery-olr"></a>OLR (원래 위치 복구)

복원 지점에서 백업을 수행한 원본 위치로 복구를 수행한 후 복구 지점에 저장 된 상태로 바꿉니다. Azure VM backup을 사용 하는 경우이는 백업을 수행한 원래 서버로 VM을 복원 하는 것을 의미 합니다. Azure 파일 공유 백업을 사용 하는 경우 백업 된 파일 공유로 데이터를 복원 하는 것을 의미 합니다.

## <a name="passphrase"></a>암호

(워크 로드 관련 용어)

암호는 MARS 에이전트를 사용 하 여 온-프레미스 또는 로컬 컴퓨터를 백업 또는 복원 하는 동안 데이터를 암호화 하 고 암호 해독 하는 데 사용 됩니다.

## <a name="private-endpoint"></a>프라이빗 엔드포인트

[개인 끝점 설명서](../private-link/private-endpoint-overview.md)를 참조 하세요.

## <a name="protected-instance"></a>보호 된 인스턴스

보호 된 인스턴스는 Azure에 백업을 구성 하는 데 사용 하는 컴퓨터, 물리적 또는 가상 서버를 나타냅니다.  **청구 관점** 에서 컴퓨터에 대 한 보호 된 인스턴스 수는 프런트 엔드 크기의 함수입니다. 따라서 단일 백업 인스턴스 (예: Azure에 백업 된 VM)는 프런트 엔드 크기에 따라 여러 보호 된 인스턴스에 해당할 수 있습니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>RBAC (역할 기반 액세스 제어)

[RBAC 설명서](../role-based-access-control/overview.md)를 참조 하세요.

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>복구 지점/복원 지점/보존 지점/지정 시간 (PIT)

백업 중인 원본 데이터의 복사본입니다. 보존 지점은 타임 스탬프와 연결 되므로이를 사용 하 여 특정 시점으로 항목을 복원할 수 있습니다.

## <a name="recovery-services-vault"></a>Recovery Services 자격 증명 모음

*Microsoft RecoveryServices/* vault 형식의 Azure Resource Manager 리소스입니다. 현재 Recovery Services 자격 증명 모음을 사용 하 여 azure vm, azure vm의 SQL, Azure Vm의 SAP HANA 및 Azure 파일 공유와 같은 작업을 백업 합니다. MARS, Azure Backup Server (MABS) 및 System Center DPM을 사용 하 여 온-프레미스 워크 로드를 백업 하는 데도 사용 됩니다. [Recovery Services 자격 증명 모음에 대해 자세히 알아보세요](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>리소스 그룹

[Azure Resource Manager 설명서](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)를 참조 하세요.

## <a name="rest-api"></a>REST API

[Azure REST API 설명서](/rest/api/azure/)를 참조 하세요.

## <a name="retention-rule"></a>보존 규칙

백업을 보존할 기간을 지정 하는 사용자 정의 규칙입니다.

## <a name="rpo-recovery-point-objective"></a>RPO (복구 지점 목표)

RPO는 데이터 손실 시나리오에서 가능한 최대 데이터 손실을 나타냅니다. 이는 백업 빈도에 따라 결정 됩니다.

## <a name="rto-recovery-time-objective"></a>RTO (복구 시간 목표)

RTO는 데이터 손실 시나리오 후에 사용 가능한 마지막 시점으로 데이터를 복원할 수 있는 최대 시간을 나타냅니다.

## <a name="scheduled-backup"></a>예약 된 백업

지정 된 항목에 대해 구성 된 백업 정책에 의해 자동으로 트리거되는 백업 작업입니다.

## <a name="secondary-region--paired-region"></a>보조 지역/쌍을 이루는 지역

지역 쌍은 동일한 지리 내의 두 지역으로 구성 됩니다. 하나는 주 지역이 고 다른 하나는 보조 지역입니다. 쌍을 이루는 지역은 일부 Azure 서비스 (GRS 설정 Azure Backup 포함)에서 비즈니스 연속성을 보장 하 고 데이터 손실을 방지 하는 데 사용 됩니다. [자세히 알아보기](../best-practices-availability-paired-regions.md).

## <a name="soft-delete"></a>일시 삭제

일시 삭제는 실수로 백업 데이터를 삭제 하지 못하도록 방지 하는 기능입니다. 일시 삭제를 사용 하는 경우 악의적인 행위자가 백업을 삭제 하는 경우 (또는 백업 데이터가 실수로 삭제 된 경우)에도 백업 데이터는 추가 시간 동안 보존 되므로 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. [자세히 알아보기](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>스냅샷

스냅숏은 VHD (가상 하드 드라이브) 또는 Azure 파일 공유의 완전 한 읽기 전용 복사본입니다. [디스크 스냅숏](../virtual-machines/windows/snapshot-copy-managed-disk.md) 및 [파일 스냅숏에](../storage/files/storage-snapshots-files.md)대해 자세히 알아보세요.

## <a name="storage-account"></a>스토리지 계정

[저장소 계정 설명서](../storage/common/storage-account-overview.md)를 참조 하세요.

## <a name="subscription"></a>구독

Azure 구독은 Azure에서 리소스를 프로비저닝하는 데 사용되는 논리적 컨테이너입니다. Vm (가상 머신), 데이터베이스 등의 모든 리소스에 대 한 세부 정보를 포함 합니다.

## <a name="system-state-backup"></a>시스템 상태 백업

(워크 로드 관련 용어)

운영 체제 파일을 백업 합니다. 이 백업을 사용 하면 컴퓨터를 시작할 때 복구할 수 있지만 시스템 파일 및 레지스트리가 손실 됩니다. [자세히 알아보기](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>테넌트

테넌트는 조직의 표현입니다. 조직 또는 앱 개발자가 Microsoft와의 관계를 만들 때(예: Azure, Microsoft Intune 또는 Microsoft 365에 등록) 조직 또는 앱 개발자가 받는 Azure AD의 전용 인스턴스입니다.

## <a name="tier"></a>계층

현재 Azure Backup는 다음과 같은 백업 저장소 계층을 지원 합니다.

### <a name="snapshot-tier"></a>스냅숏 계층

(작업 관련 용어) VM 백업의 첫 번째 단계에서 만든 스냅숏은 디스크와 함께 저장 됩니다. 이러한 형태의 저장소를 스냅숏 계층 이라고 합니다. 스냅숏 계층 복원은 복원 작업을 트리거하기 전에 자격 증명 모음에서 스냅숏을 복사 하는 대기 시간을 없애기 때문에, 자격 증명 모음에서 복원 하는 것 보다 더 빠릅니다.

### <a name="vault-standard-tier"></a>Vault-Standard 계층

Azure Backup에서 지 원하는 모든 워크 로드에 대 한 백업 데이터는 Azure Backup에서 관리 하는 저장소 계정의 자동 크기 조정 집합인 백업 저장소를 보관 하는 자격 증명 모음에 저장 됩니다. Vault-Standard 계층은 백업 데이터의 격리 된 복사본을 Microsoft 관리 되는 테 넌 트에 저장 하 여 추가 보호 계층을 만들 수 있도록 하는 온라인 저장소 계층입니다. 스냅숏 계층이 지원 되는 워크 로드의 경우 스냅숏 계층과 자격 증명 모음 표준 계층 모두에 백업 데이터의 복사본이 있습니다. 자격 증명 모음-표준 계층은 백업 되는 데이터 원본이 삭제 되거나 손상 된 경우에도 백업 데이터를 사용할 수 있도록 합니다.

## <a name="unmanaged-disk"></a>관리되지 않는 디스크

[관리 되지 않는 디스크 설명서](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks)를 참조 하세요.

## <a name="vault"></a>Vault

백업 데이터를 보관 하는 Azure의 저장소 엔터티입니다. RBAC 및 청구의 단위 이기도 합니다. 현재 자격 증명 모음 Recovery Services 자격 증명 모음 및 백업 자격 증명 모음에는 두 가지 유형이 있습니다.

## <a name="vault-credentials"></a>자격 증명 모음 자격 증명

자격 증명 모음 자격 증명 파일은 포털에서 각 자격 증명 모음에 대해 생성 하는 인증서입니다. 이는 온-프레미스 서버를 자격 증명 모음에 등록 하는 동안 사용 됩니다. [자세히 알아보기](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>VNET (Virtual Network)

[VNET 설명서](../virtual-network/virtual-networks-overview.md)를 참조 하세요.

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows 볼륨 섀도 복사본 서비스)

[VSS 설명서](/windows-server/storage/file-server/volume-shadow-copy-service)를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure Backup 개요](backup-overview.md)
- [Azure Backup 아키텍처 및 구성 요소](backup-architecture.md)