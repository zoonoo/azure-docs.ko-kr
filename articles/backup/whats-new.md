---
title: Azure Backup의 새로운 기능
description: Azure Backup의 새로운 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: c5e6734c6a962fa43d79fc90fdfaa85923b6339f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612486"
---
# <a name="whats-new-in-azure-backup"></a>Azure Backup의 새로운 기능

Azure Backup은 Azure에서 데이터의 보호 성능을 향상시키는 새로운 기능을 지속적으로 개선하며 릴리스합니다. 이러한 새 기능을 통해 데이터 보호 성능을 새로운 워크로드 유형으로 확장하고, 보안을 강화하며, 백업 데이터의 가용성을 향상시킬 수 있습니다. 여기에는 새로운 관리, 모니터링 및 자동화 기능도 추가됩니다.

이 페이지에 책갈피를 추가하거나 [여기에서 업데이트를 구독](https://azure.microsoft.com/updates/?query=backup)하여 새 릴리스에 대해 자세히 알아볼 수 있습니다.

## <a name="updates-summary"></a>업데이트 요약

- 2021년 3월
  - [이제 Azure Disk Backup이 일반 공급됨](#azure-disk-backup-is-now-generally-available)
  - [이제 백업 센터가 일반 공급됨](#backup-center-is-now-generally-available)
  - [Azure Backup용 보관 계층 지원(미리 보기)](#archive-tier-support-for-azure-backup-in-preview)
- 2021년 2월
  - [Azure Blob용 백업(미리 보기)](#backup-for-azure-blobs-in-preview)
- 2021년 1월
  - [Azure Disk Backup(미리 보기)](#azure-disk-backup-in-preview)
  - [고객 관리 키로 미사용 시 암호화(일반 공급)](#encryption-at-rest-using-customer-managed-keys)
- 2020년 11월
  - [Azure 파일 공유(AFS) 백업용 Azure Resource Manager 템플릿](#azure-resource-manager-template-for-afs-backup)
  - [Azure VM의 SAP HANA 데이터베이스에 대한 증분 백업(미리 보기)](#incremental-backups-for-sap-hana-databases-in-preview)
- 2020년 9월
  - [백업 센터(미리 보기)](#backup-center-in-preview)
  - [Azure Database for Postgre 백업(미리 보기)](#backup-azure-database-for-postgresql-in-preview)
  - [선택적 디스크 백업 및 복원](#selective-disk-backup-and-restore)
  - [Azure VM의 SQL Server 및 SAP HANA 데이터베이스에 대한 지역 간 복원(미리 보기)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [디스크가 최대 32개인 VM의 백업 지원](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Azure VM의 SQL에 간소화된 백업 구성 환경](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [RHEL Azure Virtual Machines에서 SAP HANA 백업(미리 보기)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [백업 데이터에 대한 ZRS(영역 중복 스토리지)(미리 보기)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Azure VM의 SQL Server 및 SAP HANA 워크로드에 대한 일시 삭제](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>이제 Azure Disk Backup이 일반 공급됨

Azure Backup은 백업 정책을 사용하여 스냅샷을 정기적으로 만들고 구성된 기간 동안 보존하여 Azure Managed Disks에 대한 스냅샷 수명 주기를 관리합니다.

자세한 내용은 [Azure Disk Backup 개요](disk-backup-overview.md)를 참조하세요.

## <a name="backup-center-is-now-generally-available"></a>이제 백업 센터가 일반 공급됨

백업 센터는 하나의 단일 중앙 콘솔에서 백업 관리를 검색, 제어하고 모니터링, 운영, 최적화할 수 있도록 하여 데이터 보호 관리를 대규모로 간소화합니다.

자세한 내용은 [백업 센터 개요](backup-center-overview.md)를 참조하세요.

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Azure Backup용 보관 계층 지원(미리 보기)

이제 Azure Backup을 사용하여 Azure Virtual Machines와 Azure Virtual Machines의 SQL Server에 대한 보관 계층을 사용할 수 있으며 장기 보존 백업의 비용을 줄일 수 있습니다.

자세한 내용은 [보관 계층 지원 (미리 보기)](archive-tier-support.md)을 참조하세요.

## <a name="backup-for-azure-blobs-in-preview"></a>Azure Blob용 백업(미리 보기)

Blob에 대한 운영 백업은 손상, Blob 삭제 및 실수로 인한 스토리지 계정 삭제와 같은 다양한 데이터 손실 시나리오에서 블록 Blob을 보호할 수 있는 관리형 로컬 데이터 보호 솔루션입니다. 데이터는 원본 스토리지 계정에 로컬로 저장되며 필요할 때마다 선택한 시점으로 복구할 수 있습니다. 따라서 Blob을 보호하는 간단하고 안전하며 비용 효과적인 방법을 제공합니다.

Blob에 대한 운영 백업은 다른 Backup 관리 기능 중에서 백업 센터와 통합되어 대규모로 백업을 제어, 모니터링, 운영 및 분석하는 데 유용한 단일 창을 제공합니다.

자세한 내용은 [Azure Blob용 운영 백업 개요(미리 보기)](blob-backup-overview.md)를 참조하세요.

## <a name="azure-disk-backup-in-preview"></a>Azure Disk Backup(미리 보기)

Azure Disk Backup은 스냅샷 생성을 정기적으로 자동화하고 백업 정책을 사용하여 구성된 기간 동안 유지함으로써 [Azure Managed Disks](../virtual-machines/managed-disks-overview.md)에 대한 스냅샷 수명 주기를 관리하는 턴키 솔루션을 제공합니다. 인프라 비용이 0이고 사용자 지정 스크립팅 또는 관리 오버헤드가 없어도 디스크 스냅샷을 관리할 수 있습니다. 매일 여러 백업에 대한 지원을 포함하는 [증분 스냅샷](../virtual-machines/disks-incremental-snapshots.md)을 사용하여 관리 디스크의 지정 시간 백업을 수행하는 충돌 일관성 백업 솔루션입니다. 에이전트 없는 솔루션이기도 하며, 프로덕션 애플리케이션 성능에 영향을 주지 않습니다. 현재 실행 중인 Azure 가상 머신에 연결되어 있는지 여부에 관계없이 OS 및 데이터 디스크(공유 디스크 포함)의 백업과 복원을 지원합니다.

자세한 내용은 [Azure Disk Backup(미리 보기)](disk-backup-overview.md)을 참조하세요.

## <a name="encryption-at-rest-using-customer-managed-keys"></a>고객 관리형 키로 미사용 시 암호화

이제 고객 관리형 키로 미사용 시 암호화를 지원하는 기능이 일반적으로 공급됩니다. 이를 통해 Azure Key Vault에 저장된 고유한 키로 Recovery Services 자격 증명 모음에서 백업 데이터를 암호화할 수 있습니다. Recovery Services 자격 증명 모음의 백업 암호화에 사용되는 암호화 키는 원본 암호화에 사용되는 키와 다를 수 있습니다. 데이터는 AES 256 기반 DEK(데이터 암호화 키)를 사용하여 보호되며, 이는 Key Vault에 저장된 키를 사용하여 보호됩니다. 플랫폼 관리형 키를 사용하는 암호화(기본적으로 사용 가능)와 비교해 보면, 키 제어 성능을 더 많이 제공하고 규정 준수 요구 사항을 더 잘 충족하는 데 도움이 될 수 있습니다.

자세한 내용은 [고객 관리형 키를 사용하여 백업 데이터 암호화](encryption-at-rest-with-cmk.md)를 참조하세요.

## <a name="azure-resource-manager-template-for-afs-backup"></a>AFS 백업용 Azure Resource Manager 템플릿

이제 Azure Backup은 ARM(Azure Resource Manager) 템플릿을 사용하여 기존 Azure 파일 공유에 대한 백업 구성을 지원합니다. 이 템플릿은 Recovery Services 자격 증명 모음 및 백업 정책에 적절한 세부 정보를 지정하여 기존 Azure 파일 공유에 대한 보호를 구성합니다. 필요에 따라 새 Recovery Services 자격 증명 모음 및 백업 정책을 만들고 파일 공유를 포함하는 스토리지 계정을 Recovery Services 자격 증명 모음에 등록합니다.

자세한 내용은 [Azure Backup용 Azure Resource Manager 템플릿](backup-rm-template-samples.md)을 참조하세요.

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>SAP HANA 데이터베이스용 증분 백업(미리 보기)

이제 Azure Backup은 Azure VM에서 호스트되는 SAP HANA 데이터베이스용 증분 백업을 지원합니다. 이를 통해 SAP HANA 데이터를 보다 빠르고 비용 효율적으로 백업할 수 있습니다.

자세한 내용은 [백업 정책을 만드는 동안 사용할 수 있는 다양한 옵션](sap-hana-faq-backup-azure-vm.md#policy) 및 [SAP HANA 데이터베이스용 백업 정책을 만드는 방법](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)을 참조하세요.

## <a name="backup-center-in-preview"></a>백업 센터(미리 보기)

Azure Backup은 중앙 콘솔에서 전체 백업 공간을 관리하는 새로운 기본 관리 기능이 사용하도록 설정되었습니다. 백업 센터에서는 Azure의 기본 관리 환경과 일관성 있는 통합된 방식으로 데이터 보호를 대규모로 모니터링, 운영, 관리하고 최적화할 수 있는 기능을 제공합니다.

백업 센터를 사용하면 Azure Lighthouse를 통해 구독, 위치, 리소스 그룹, 자격 증명 모음 및 테넌트에 대해 집계된 인벤토리 보기를 얻을 수 있습니다. 백업 센터는 단일 위치에서 백업 구성, 복원, 정책 또는 자격 증명 모음 만들기 같은 백업 관련 활동을 트리거할 수 있는 작업 센터이기도 합니다. 또한 Azure Policy를 원활하게 통합하여 이제 환경을 관리하고 백업 관점에서 호환성을 추적할 수 있습니다. Azure Backup과 관련된 기본 제공 Azure Policy를 사용하여 백업을 대규모로 구성할 수도 있습니다.

자세한 내용은 [백업 센터 개요](backup-center-overview.md)를 참조하세요.

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Azure Database for PostgreSQL 백업(미리 보기)

Azure Backup과 Azure Data Services가 결합되어 Azure PostgreSQL(현재 미리 보기)용 엔터프라이즈급 백업 솔루션을 빌드할 수 있습니다. 이제 최장 10년간 백업 보존을 사용하도록 설정하는 고객 제어형 백업 정책을 통해 데이터 보호 및 규정 준수 요구 사항을 충족할 수 있습니다. 이를 통해 개별 데이터베이스 수준에서 백업/복원 작업을 관리하는 세분화된 제어를 사용할 수 있습니다. 마찬가지로, PostgreSQL 버전 또는 Blob 스토리지 간에 쉽게 복원할 수 있습니다.

자세한 내용은 [Azure Database for PostgreSQL 백업](backup-azure-database-postgresql.md)을 참조하세요.

## <a name="selective-disk-backup-and-restore"></a>선택적 디스크 백업 및 복원

Azure Backup은 가상 머신 백업 솔루션을 함께 사용하여 VM의 모든 디스크(운영 체제 및 데이터) 백업 작업을 지원합니다. 이제 선택적 디스크 백업 및 복원 기능을 사용하여 VM에서 데이터 디스크의 하위 집합을 백업할 수 있습니다. 이는 사용자의 백업 및 복원 요구 사항을 충족하는 능률적이고 비용 효율적인 솔루션을 제공합니다. 각 복구 지점에는 백업 작업에 포함되는 디스크만 포함됩니다.

자세한 내용은 [Azure Virtual Machines용 선택적 디스크 백업 및 복원](selective-disk-backup-restore.md)을 참조하세요.

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>SQL Server 및 SAP HANA용 지역 간 복원(미리 보기)

지역 간 복원을 도입함에 따라 이제 보조 지역에서 복원을 시작하여 사용자 환경의 주 지역에서 일어나는 실제 가동 중지 시간 문제를 완화할 수 있습니다. 이렇게 하면 보조 지역 복원을 고객이 완전히 제어할 수 있습니다. Azure Backup은 해당 복원 시 보조 지역에 복제된 백업 데이터를 사용합니다.

이제 이 기능은 Azure Virtual Machines용 지역 간 복원을 지원할 뿐만 아니라, Azure Virtual Machines에서 SQL 및 SAP HANA 데이터베이스를 복원하도록 확장되었습니다.

자세한 내용은 [SQL 데이터베이스용 지역 간 복원](restore-sql-database-azure-vm.md#cross-region-restore) 및 [SAP HANA 데이터베이스용 지역 간 복원](sap-hana-db-restore.md#cross-region-restore)을 참조하세요.

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>디스크가 최대 32개인 VM의 백업 지원

지금까지 Azure Backup은 VM당 16개의 관리 디스크를 지원했습니다. 이제 Azure Backup은 VM당 최대 32개의 관리 디스크 백업을 지원합니다.

자세한 내용은 [VM 스토리지 지원 매트릭스](backup-support-matrix-iaas.md#vm-storage-support)를 참조하세요.

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Azure VM의 SQL용 백업 구성 간소화

이제 Azure Portal의 VM 창에 통합된 인라인 백업 구성을 사용하여 Azure VM에서 SQL Server용 백업을 구성하기가 훨씬 쉬워졌습니다. 몇 가지 단계를 통해 SQL Server 백업을 사용하도록 설정함으로써 기존 데이터베이스뿐만 아니라 나중에 추가되는 데이터베이스를 보호할 수 있습니다.

자세한 내용은 [VM 창에서 SQL Server 백업](backup-sql-server-vm-from-vm-pane.md)을 참조하세요.

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>RHEL Azure Virtual Machines에서 SAP HANA 백업(미리 보기)

Azure Backup은 Azure의 기본 백업 솔루션으로, SAP에서 BackInt 인증을 받았습니다. 현재 Azure Backup에는 SAP HANA를 실행하여 가장 널리 사용되는 Linux 운영 체제 중 하나인 Red Hat Enterprise Linux(RHEL)에 대한 지원이 추가되었습니다.

자세한 내용은 [SAP HANA 데이터베이스 백업 시나리오 지원 매트릭스](sap-hana-backup-support-matrix.md#scenario-support)를 참조하세요.

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>백업 데이터에 대한 ZRS(영역 중복 스토리지)(미리 보기)

Azure Storage는 다양한 중복도 옵션을 통해 고성능과 고가용성, 높은 데이터 복원력을 제공합니다. Azure Backup를 사용하면 LRS(로컬 중복 스토리지) 및 GRS(지역 중복 스토리지)에 백업을 저장하는 옵션을 통해 백업 데이터에서도 이러한 성능을 얻을 수 있습니다. 현재 ZRS(영역 중복 스토리지)에 대한 지원이 더해진 내구성 옵션도 추가로 제공됩니다.

자세한 내용은 [Recovery Services 자격 증명 모음에 대한 스토리지 중복도 설정](backup-create-rs-vault.md#set-storage-redundancy)을 참조하세요.

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server 및 SAP HANA 워크로드에 대한 일시 삭제

맬웨어, 랜섬웨어 및 침입과 같은 보안 문제에 대한 우려는 증가하고 있습니다. 이러한 보안 문제는 돈과 데이터 측면 모두에서 비용이 많이 들 수 있습니다. 이러한 공격을 방지하기 위해 Azure Backup은 삭제 후에도 백업 데이터를 보호하는 데 도움이 되는 보안 기능을 제공합니다.

이러한 기능 중 하나가 바로 일시 삭제입니다. 일시 삭제를 사용하면 악의적인 행위자가 백업을 삭제하거나 백업 데이터가 실수로 삭제된 경우에도 백업 데이터가 추가로 14일간 보관되므로 데이터 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대한 추가 보존 기간은 14일이며 비용이 발생하지 않습니다.

현재 Azure VM에 대한 일시 삭제를 지원할 뿐만 아니라, Azure VM의 SQL Server 및 SAP HANA 워크로드도 일시 삭제로 보호합니다.

자세한 내용은 [Azure VM의 SQL Server 일시 삭제 및 Azure VM 워크로드의 SAP HANA 일시 삭제](soft-delete-sql-saphana-in-azure-vm.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Backup 지침 및 모범 사례](guidance-best-practices.md)