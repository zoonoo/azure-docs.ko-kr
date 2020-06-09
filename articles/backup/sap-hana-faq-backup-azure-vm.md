---
title: FAQ - Azure VM의 SAP HANA 데이터베이스 백업
description: 이 문서에서는 Azure Backup 서비스를 사용하여 SAP HANA 데이터베이스를 백업하는 방법에 대한 일반적인 질문과 답변을 제공합니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 56f98dddb00eb3ffc87eb27da73066de807a1ee1
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701018"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>자주 묻는 질문 – Azure VM의 SAP HANA 데이터베이스 백업

이 문서에서는 Azure Backup 서비스를 사용하여 SAP HANA 데이터베이스를 백업하는 방법에 대한 일반적인 질문과 답변을 제공합니다.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>하루에 지원되는 전체 백업은 몇 건인가요?

하루에 한 건의 전체 백업만 지원됩니다. 차등 백업과 전체 백업을 같은 날에 트리거할 수 없습니다.

### <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다. 포털 경고의 상세한 동작은 [여기](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)에서 확인하세요. 성공한 작업에 대해서도 경고를 받으려면 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)를 사용하면 됩니다.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>백업 작업 메뉴에서 예약된 백업 작업을 볼 수 있나요?

백업 작업 메뉴에는 임시 백업 작업만 표시됩니다. 예약된 작업을 보려면 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)를 사용하세요.

### <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?

현재 이 기능은 지원되지 않습니다.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>인스턴스에서 데이터베이스를 삭제하면 백업은 어떻게 되나요?

SAP HANA 인스턴스에서 데이터베이스를 삭제해도 해당 데이터베이스의 백업이 계속 시도됩니다. 즉, 삭제된 데이터베이스가 **백업 항목**에서 비정상 상태로 표시되기 시작하지만 여전히 보호되는 것으로 처리됩니다.
이 데이터베이스의 보호를 중지하는 올바른 방법은 이 데이터베이스에 대해 **데이터 삭제 시 백업 중지**를 수행하는 것입니다.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>보호된 데이터베이스의 이름을 변경하면 어떻게 되나요?

이름이 변경된 데이터베이스는 새 데이터베이스로 처리됩니다. 따라서 서비스는 이 상황을 해당 데이터베이스가 발견되지 않은 것처럼 처리하고 백업을 실패 처리합니다. 이름이 변경된 데이터베이스는 새 데이터베이스로 표시되며 보호받도록 구성되어야 합니다.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>SAP HANA 데이터베이스를 Azure VM에 백업하기 위한 전제 조건은 무엇인가요?

[전제 조건](tutorial-backup-sap-hana-db.md#prerequisites) 및 [사전 등록 스크립트의 기능](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 섹션을 참조하세요.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Azure에서 SAP HANA 데이터베이스를 백업하려면 어떤 권한을 설정해야 하나요?

사전 등록 스크립트를 실행하면 Azure에서 SAP HANA 데이터베이스 백업을 허용하는 데 필요한 권한이 설정됩니다. [여기](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)에서 사전 등록 스크립트가 어떤 작업을 수행하는지 자세히 알아볼 수 있습니다.

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>SAP HANA를 SDC에서 MDC로 마이그레이션한 후에도 백업이 작동하나요?

문제 해결 가이드의 [이 섹션](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid)을 참조하세요.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>가상 머신이 아닌 가상 IP(부하 분산 장치)에 대해 Azure HANA 백업을 설정할 수 있나요?

현재 가상 IP에 대해 단독으로 솔루션을 설정하는 기능은 지원되지 않습니다. 솔루션을 실행하려면 가상 머신이 필요합니다.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>SAP HSR(HANA System Replication)이 있는데, 이 설정에 대해 백업을 어떻게 구성해야 하나요?

HSR의 기본 노드와 보조 노드는 서로 관련이 없는 두 개의 VM으로 취급됩니다. 기본 노드에서 백업을 구성하고, 장애 조치(failover)가 이루어지면 기본 노드가 되는 보조 노드에서 백업을 구성해야 합니다. 백업이 다른 노드로 자동으로 ‘장애 조치(failover)’되지 않습니다.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>주문형 백업을 Azure 자격 증명 모음이 아닌 로컬 파일 시스템으로 이동할 수 있나요?

1. 원하는 데이터베이스에 대해 현재 실행 중인 백업이 완료될 때까지 기다리세요(스튜디오에서 완료 여부를 확인하세요).
1. 다음 단계에 따라 원하는 DB에 대해 로그 백업을 사용하지 않도록 설정하고 카탈로그 백업을 **파일 시스템**으로 설정합니다.
1. **SYSTEMDB** -> **구성** -> **데이터베이스 선택** -> **필터(로그)** 를 두 번 클릭합니다.
    1. enable_auto_log_backup을 **아니요**로 설정합니다.
    1. log_backup_using_backint를 **false**로 설정합니다.
1. 원하는 데이터베이스에 대해 주문형 백업을 수행하고, 백업과 카탈로그 백업이 완료될 때까지 기다립니다.
1. 백업이 Azure 자격 증명 모음으로 흐를 수 있도록 이전 설정으로 되돌립니다.
    1. enable_auto_log_backup을 **예**로 설정합니다.
    1. log_backup_using_backint를 **true**로 설정합니다.

## <a name="restore"></a>복원

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>내 데이터베이스를 복원하려는 HANA 시스템이 표시되지 않는 이유는 무엇인가요?

대상 SAP HANA 인스턴스로 복원하기 위한 모든 전제 조건이 충족되었는지 확인하세요. 자세한 내용은 [전제 조건 - Azure VM에서 SAP HANA 데이터베이스 복원](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)을 참조하세요.

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>데이터베이스에 대한 덮어쓰기 DB 복원이 실패하는 이유는 무엇인가요?

복원하는 동안 **무조건 덮어쓰기** 옵션이 선택되어 있는지 확인하세요.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>“복원의 원본 및 대상 시스템이 호환되지 않습니다” 오류가 표시되는 이유는 무엇인가요?

현재 지원되는 복원 유형은 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행되는 [SAP HANA 데이터베이스를 백업](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)하는 방법을 알아봅니다.
