---
title: Azure Backup의 새로운 기능
description: Azure Backup의 새로운 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: e48a7df1e42591843c30b77026a4d8656773a57d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511596"
---
# <a name="whats-new-in-azure-backup"></a>Azure Backup의 새로운 기능

Azure Backup은 Azure에서 데이터의 보호를 향상 시키는 새로운 기능을 지속적으로 개선 하 고 릴리스 합니다. 이러한 새로운 기능을 통해 데이터 보호를 새로운 작업 유형으로 확장 하 고, 보안을 강화 하 고, 백업 데이터의 가용성을 향상 시킬 수 있습니다. 또한 새 관리, 모니터링 및 자동화 기능을 추가 합니다.

이 페이지에 책갈피를 추가 하거나 [여기에서 업데이트를 구독](https://azure.microsoft.com/updates/?query=backup)하 여 새 릴리스에 대해 자세히 알아볼 수 있습니다.

## <a name="updates-summary"></a>업데이트 요약

- 2020년 11월
  - [Azure Vm의 SAP HANA 데이터베이스에 대 한 증분 백업](#incremental-backups-for-sap-hana-databases)
- 2020년 9월
  - [백업 센터](#backup-center)
  - [Azure Database for PostgreSQL 백업](#backup-azure-database-for-postgresql)
  - [선택적 디스크 백업 및 복원](#selective-disk-backup-and-restore)
  - [Azure Vm의 SQL Server 및 SAP HANA 데이터베이스에 대 한 지역 간 복원](#cross-region-restore-for-sql-server-and-sap-hana)
  - [최대 32 디스크를 포함 하는 Vm의 백업 지원](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Azure Vm의 SQL에 대 한 간소화 된 백업 구성 환경](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [RHEL Azure Virtual Machines의 백업 SAP HANA](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [백업 데이터에 대 한 ZRS (영역 중복 저장소)](#zone-redundant-storage-zrs-for-backup-data)
  - [Azure Vm의 SQL Server 및 SAP HANA 작업에 대 한 일시 삭제](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="incremental-backups-for-sap-hana-databases"></a>SAP HANA 데이터베이스에 대 한 증분 백업

이제 Azure Backup은 Azure Vm에서 호스트 되는 SAP HANA 데이터베이스에 대 한 증분 백업을 지원 합니다. 이를 통해 SAP HANA 데이터를 보다 빠르고 비용 효율적으로 백업할 수 있습니다.

자세한 내용은 [백업 정책을 만드는 동안 사용할 수 있는 다양 한 옵션](sap-hana-faq-backup-azure-vm.md#policy) 및 [SAP HANA 데이터베이스에 대 한 백업 정책을 만드는 방법](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)을 참조 하세요.

## <a name="backup-center"></a>백업 센터

Azure Backup는 중앙 콘솔에서 전체 백업 공간을 관리 하는 새로운 네이티브 관리 기능을 사용 하도록 설정 했습니다. 백업 센터에서는 Azure의 기본 관리 환경과 일관 된 통합 방식으로 데이터 보호를 대규모로 모니터링, 운영, 관리 및 최적화할 수 있는 기능을 제공 합니다.

Backup Center를 사용 하 여 구독, 위치, 리소스 그룹, 자격 증명 모음 및 Azure Lighthouse를 사용 하는 테 넌 트에 걸친 재고의 집계 보기를 얻을 수 있습니다. 백업 센터는 단일 위치에서 백업, 복원, 정책 또는 자격 증명 모음 만들기와 같은 백업 관련 작업을 트리거할 수 있는 작업 센터 이기도 합니다. 또한 Azure Policy를 원활 하 게 통합 하 여 이제 환경을 관리 하 고 백업 관점에서 호환성을 추적할 수 있습니다. Azure Backup 관련 된 빌드된 Azure 정책을 사용 하 여 대규모로 백업을 구성할 수도 있습니다.

자세한 내용은 [백업 센터 개요](backup-center-overview.md)를 참조 하세요.

## <a name="backup-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 백업

Azure Backup 및 Azure 데이터베이스 서비스는 Azure PostgreSQL (현재 미리 보기로 제공)에 대 한 엔터프라이즈급 백업 솔루션을 구축 하는 데 함께 제공 됩니다. 이제 최대 10 년 동안 백업 보존을 가능 하 게 하는 고객이 제어 하는 백업 정책을 사용 하 여 데이터 보호 및 규정 준수 요구 사항을 충족할 수 있습니다. 이를 통해 개별 데이터베이스 수준에서 백업 및 복원 작업을 관리 하는 세분화 된 제어를 사용할 수 있습니다. 마찬가지로, PostgreSQL 버전 또는 blob 저장소 간에 쉽게 복원할 수 있습니다.

자세한 내용은 [Azure Database for PostgreSQL backup](backup-azure-database-postgresql.md)을 참조 하세요.

## <a name="selective-disk-backup-and-restore"></a>선택적 디스크 백업 및 복원

Azure Backup는 가상 컴퓨터 백업 솔루션을 사용 하 여 VM의 모든 디스크 (운영 체제 및 데이터)를 백업 하는 작업을 지원 합니다. 이제 선택적 디스크 백업 및 복원 기능을 사용 하 여 VM에서 데이터 디스크의 하위 집합을 백업할 수 있습니다. 이는 사용자의 백업 및 복원 요구 사항을 충족하는 능률적이고 비용 효율적인 솔루션을 제공합니다. 각 복구 지점에는 백업 작업에 포함 된 디스크만 포함 됩니다.

자세한 내용은 [Azure 가상 컴퓨터에 대 한 선택적 디스크 백업 및 복원](selective-disk-backup-restore.md)을 참조 하세요.

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>SQL Server 및 SAP HANA에 대 한 지역 간 복원

지역 간 복원의 도입으로 이제는 보조 지역에서 복원을 시작 하 여 사용자 환경에 대 한 기본 지역에서의 실제 가동 중지 시간 문제를 완화할 수 있습니다. 이렇게 하면 보조 지역은 완전히 고객 제어를 복원 합니다. Azure Backup은 해당 복원에 대해 보조 지역에 복제 된 백업 된 데이터를 사용 합니다.

이제 Azure 가상 컴퓨터에 대 한 지역 간 복원을 지원 하는 것 외에도이 기능은 Azure virtual machines에서 SQL 및 SAP HANA 데이터베이스를 복원 하도록 확장 되었습니다.

자세한 내용은 [SQL database에 대 한 지역 간 복원](restore-sql-database-azure-vm.md#cross-region-restore) 및 [SAP HANA 데이터베이스에 대 한 지역 간 복원](sap-hana-db-restore.md#cross-region-restore)을 참조 하세요.

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>최대 32 디스크를 포함 하는 Vm의 백업 지원

지금까지 Azure Backup은 VM 당 16 개의 managed disks를 지원 합니다. 이제 Azure Backup는 VM 당 최대 32 개의 managed disks 백업을 지원 합니다.

자세한 내용은 [VM 저장소 지원 매트릭스](backup-support-matrix-iaas.md#vm-storage-support)를 참조 하세요.

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Azure Vm의 SQL에 대 한 간단한 백업 구성

이제 Azure Portal의 VM 창에 통합 된 인라인 백업 구성을 사용 하 여 Azure Vm에서 SQL Server에 대 한 백업을 구성 하기가 훨씬 쉬워졌습니다. 몇 가지 단계를 수행 하 여 기존 데이터베이스 뿐만 아니라 나중에 추가 되는 데이터베이스를 보호 하는 SQL Server의 백업을 사용할 수 있습니다.

자세한 내용은 [VM 창에서 SQL Server 백업](backup-sql-server-vm-from-vm-pane.md)을 참조 하세요.

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>RHEL Azure virtual machines의 백업 SAP HANA

Azure Backup은 Azure에 대 한 기본 백업 솔루션으로, SAP에서 온-Int로 인증 됩니다. Azure Backup에서 SAP HANA를 실행 하는 가장 널리 사용 되는 Linux 운영 체제 중 하나인 Red Hat Enterprise Linux (RHEL)에 대 한 지원이 추가 되었습니다.

자세한 내용은 [SAP HANA 데이터베이스 백업 시나리오 지원 매트릭스](sap-hana-backup-support-matrix.md#scenario-support)를 참조 하세요.

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>백업 데이터에 대 한 ZRS (영역 중복 저장소)

Azure Storage은 다양 한 중복성 옵션을 통해 높은 성능, 고가용성 및 높은 데이터 복원 력을 제공 합니다. Azure Backup를 사용 하면 백업을 LRS (로컬 중복 저장소) 및 GRS (지역 중복 저장소)에 저장 하는 옵션을 사용 하 여 이러한 혜택을 백업 데이터로 확장할 수 있습니다. 이제 ZRS (영역 중복 저장소)에 대 한 추가 지원 기능이 포함 된 추가 내구성 옵션이 있습니다.

자세한 내용은 [Recovery Services 자격 증명 모음에 대 한 저장소 중복성 설정](backup-create-rs-vault.md#set-storage-redundancy)을 참조 하세요.

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server 및 SAP HANA 워크 로드에 대 한 일시 삭제

맬웨어, 랜섬웨어 및 침입과 같은 보안 문제에 대한 우려는 증가하고 있습니다. 이러한 보안 문제는 돈과 데이터 측면 모두에서 비용이 많이 들 수 있습니다. 이러한 공격 으로부터 보호 하기 위해 Azure Backup는 삭제 후에도 백업 데이터를 보호 하는 데 도움이 되는 보안 기능을 제공 합니다.

이러한 기능 중 하나는 일시 삭제입니다. 일시 삭제를 사용 하는 경우 악의적인 행위자가 백업을 삭제 하는 경우 (또는 백업 데이터가 실수로 삭제 된 경우에도), 백업 데이터는 14 일 동안 보존 되므로 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대 한 14 일의 추가 보존 기간은 아무런 비용이 들지 않습니다.

이제 azure Vm에 대 한 일시 삭제 지원 외에도 Azure Vm의 SQL Server 및 SAP HANA 작업을 일시 삭제로 보호 합니다.

자세한 내용은 [AZURE vm의 SQL server에 대 한 소프트 삭제 및 AZURE vm 워크 로드의 SAP HANA](soft-delete-sql-saphana-in-azure-vm.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Backup 지침 및 모범 사례](guidance-best-practices.md)
