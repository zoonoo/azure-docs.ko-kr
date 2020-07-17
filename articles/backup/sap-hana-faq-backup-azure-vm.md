---
title: FAQ - Azure VM의 SAP HANA 데이터베이스 백업
description: 이 문서에서는 Azure Backup 서비스를 사용하여 SAP HANA 데이터베이스를 백업하는 방법에 대한 일반적인 질문과 답변을 제공합니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 512075a24cf9400415f2367ead16b57f8b31c038
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170329"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>자주 묻는 질문 – Azure VM의 SAP HANA 데이터베이스 백업

이 문서에서는 Azure Backup 서비스를 사용하여 SAP HANA 데이터베이스를 백업하는 방법에 대한 일반적인 질문과 답변을 제공합니다.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>하루에 지원되는 전체 백업은 몇 건인가요?

하루에 한 건의 전체 백업만 지원됩니다. 차등 백업 및 전체 백업은 같은 날짜에 트리거될 수 없습니다.

### <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다. 포털 경고의 상세한 동작은 [여기](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)에서 확인하세요. 그러나 성공한 작업에 대 한 경고를 포함 하는 경우에는 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)를 사용할 수 있습니다.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>백업 작업 메뉴에서 예약된 백업 작업을 볼 수 있나요?

백업 작업 메뉴에는 임시 백업 작업만 표시됩니다. 예약된 작업을 보려면 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)를 사용하세요.

### <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?

아니요, 현재 지원 되지 않습니다.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>인스턴스에서 데이터베이스를 삭제하면 백업은 어떻게 되나요?

SAP HANA 인스턴스에서 데이터베이스를 삭제해도 해당 데이터베이스의 백업이 계속 시도됩니다. 즉, 삭제된 데이터베이스가 **백업 항목**에서 비정상 상태로 표시되기 시작하지만 여전히 보호되는 것으로 처리됩니다.
이 데이터베이스의 보호를 중지하는 올바른 방법은 이 데이터베이스에 대해 **데이터 삭제 시 백업 중지**를 수행하는 것입니다.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>보호된 데이터베이스의 이름을 변경하면 어떻게 되나요?

이름이 변경된 데이터베이스는 새 데이터베이스로 처리됩니다. 따라서 서비스는 데이터베이스를 찾을 수 없고 백업에 실패 한 것 처럼이 상황을 처리 합니다. 이름이 변경된 데이터베이스는 새 데이터베이스로 표시되며 보호받도록 구성되어야 합니다.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>SAP HANA 데이터베이스를 Azure VM에 백업하기 위한 전제 조건은 무엇인가요?

[전제 조건](tutorial-backup-sap-hana-db.md#prerequisites) 및 [사전 등록 스크립트의 기능](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 섹션을 참조하세요.

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Azure에서 SAP HANA 데이터베이스를 백업할 수 있도록 설정 해야 하는 사용 권한은 무엇 인가요?

사전 등록 스크립트를 실행하면 Azure에서 SAP HANA 데이터베이스 백업을 허용하는 데 필요한 권한이 설정됩니다. [여기](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)에서 사전 등록 스크립트가 어떤 작업을 수행하는지 자세히 알아볼 수 있습니다.

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>SAP HANA를 SDC에서 MDC로 마이그레이션한 후에도 백업이 작동하나요?

문제 해결 가이드의 [이 섹션](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#sdc-to-mdc-upgrade-with-a-change-in-sid)을 참조하세요.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>가상 머신이 아닌 가상 IP(부하 분산 장치)에 대해 Azure HANA 백업을 설정할 수 있나요?

현재는 가상 IP에 대해서만 솔루션을 설정 하는 기능이 없습니다. 솔루션을 실행하려면 가상 머신이 필요합니다.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>주문형 백업을 Azure 자격 증명 모음이 아닌 로컬 파일 시스템으로 이동할 수 있나요?

1. 원하는 데이터베이스에서 현재 실행 중인 백업이 완료 될 때까지 기다립니다 (studio에서 완료를 확인).
1. 다음 단계에 따라 원하는 DB에 대해 로그 백업을 사용하지 않도록 설정하고 카탈로그 백업을 **파일 시스템**으로 설정합니다.
1. **SYSTEMDB** -> **구성** -> **데이터베이스 선택** -> **필터(로그)** 를 두 번 클릭합니다.
    1. enable_auto_log_backup을 **아니요**로 설정합니다.
    1. Catalog_backup_using_backint을 **false** 로 설정 합니다.
1. 원하는 데이터베이스에서 주문형 백업 (전체/차등/증분)을 수행 하 고 백업 및 카탈로그 백업이 완료 될 때까지 기다립니다.
1. 로그 백업을 Filesystem로 이동 하려면 enable_auto_log_backup를 **예** 로 설정 합니다.
1. 백업이 Azure 자격 증명 모음으로 흐를 수 있도록 이전 설정으로 되돌립니다.
    1. enable_auto_log_backup을 **예**로 설정합니다.
    1. Catalog_backup_using_backint를 **true** 로 설정 합니다.

>[!NOTE]
>백업을 로컬 파일 시스템으로 이동 하 고 다시 Azure vault로 다시 전환 하면 자격 증명 모음에서 로그 백업의 로그 체인이 끊어질 수 있습니다. 이렇게 하면 전체 백업이 트리거되고 성공적으로 완료 되 면 로그 백업이 시작 됩니다.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>내 HANA 복제 설정으로 SAP HANA 백업을 사용 하려면 어떻게 하나요?

현재 Azure Backup에는 HSR 설정을 이해할 수 있는 기능이 없습니다. 즉, HSR의 기본 및 보조 노드는 관련이 없는 두 개의 개별 Vm으로 처리 됩니다. 먼저 주 노드에서 백업을 구성 해야 합니다. 장애 조치 (failover)가 발생 하면 보조 노드 (이제 주 노드가 됨)에 백업을 구성 해야 합니다. 다른 노드에 대 한 백업 자동 장애 조치 (failover)는 수행 되지 않습니다.

지정 된 시점에 활성 (주) 노드의 데이터를 백업 하려면 장애 조치 (failover) 후 주 노드로 **보호를 전환할** 수 있습니다.

이 **스위치 보호**를 수행 하려면 다음 단계를 수행 합니다.

- 주 서버의 [보호 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (데이터 보존)
- 보조 노드에서 [등록 전 스크립트](https://aka.ms/scriptforpermsonhana) 실행
- 보조 노드에서 [데이터베이스를 검색](tutorial-backup-sap-hana-db.md#discover-the-databases) 하 고 [백업 구성](tutorial-backup-sap-hana-db.md#configure-backup)

이러한 단계는 장애 조치 (failover) 후에 수동으로 수행 해야 합니다. Azure Portal와 함께 명령줄/HTTP REST를 통해 이러한 단계를 수행할 수 있습니다. 이러한 단계를 자동화 하려면 Azure runbook을 사용할 수 있습니다.

**스위치 보호** 를 수행 해야 하는 방법에 대 한 자세한 예는 다음과 같습니다.

이 예에서는 HSR 설정에 노드 1 (주) 및 노드 2 (보조) 노드가 두 개 있습니다.  백업은 노드 1에 구성 됩니다. 위에서 설명한 것 처럼 노드 2에서 백업을 구성 하는 것을 아직 시도 하지 마세요.

첫 번째 장애 조치 (failover)가 발생 하면 노드 2가 주 서버가 됩니다. 그렇다면

1. 데이터 보존 옵션을 사용 하 여 노드 1 (이전 주)의 보호를 중지 합니다.
1. 노드 2 (이제는 주)에서 사전 등록 스크립트를 실행 합니다.
1. 노드 2에서 데이터베이스를 검색 하 고, 백업 정책을 할당 하 고, 백업을 구성 합니다.

그런 다음 첫 번째 전체 백업이 노드 2에서 트리거되고 완료 된 후에 로그 백업이 시작 됩니다.

다음 장애 조치 (failover)가 발생 하면 노드 1이 다시 주 서버가 되 고 노드 2가 보조가 됩니다. 이제 프로세스를 반복 합니다.

1. 데이터 보존 옵션을 사용 하 여 노드 2의 보호를 중지 합니다.
1. 노드 1 (주 복제본이 됨)에서 사전 등록 스크립트를 실행 합니다.
1. 그런 다음 필요한 정책으로 노드 1에서 [백업을 다시 시작](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) 합니다 (백업이 노드 1에서 일찍 중지 됨).

그런 다음 노드 1에서 전체 백업이 다시 트리거되고이 작업이 완료 된 후에는 로그 백업이 시작 됩니다.

## <a name="restore"></a>복원

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>내 데이터베이스를 복원하려는 HANA 시스템이 표시되지 않는 이유는 무엇인가요?

대상 SAP HANA 인스턴스로 복원하기 위한 모든 전제 조건이 충족되었는지 확인하세요. 자세한 내용은 [전제 조건 - Azure VM에서 SAP HANA 데이터베이스 복원](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)을 참조하세요.

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>데이터베이스에 대한 덮어쓰기 DB 복원이 실패하는 이유는 무엇인가요?

복원하는 동안 **무조건 덮어쓰기** 옵션이 선택되어 있는지 확인하세요.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>“복원의 원본 및 대상 시스템이 호환되지 않습니다” 오류가 표시되는 이유는 무엇인가요?

현재 지원되는 복원 유형은 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)을 참조하세요.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>SLES에서 실행 되는 데이터베이스의 백업을 사용 하 여 RHEL HANA 시스템으로 복원 하거나 그 반대로 사용할 수 있나요?

예, SLES에서 실행 되는 HANA 데이터베이스에서 트리거되는 스트리밍 백업을 사용 하 여 RHEL HANA 시스템으로 복원 하 고 그 반대의 경우도 마찬가지입니다. 즉, 스트리밍 백업을 사용 하 여 OS 간 복원을 수행할 수 있습니다. 그러나 복원 하려는 HANA 시스템 및 복원에 사용 되는 HANA 시스템이 SAP에 따라 복원에 대해 모두 호환 되는지 확인 해야 합니다. 호환 되는 복원 유형을 확인 하려면 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행되는 [SAP HANA 데이터베이스를 백업](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)하는 방법을 알아봅니다.
