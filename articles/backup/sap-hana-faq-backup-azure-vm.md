---
title: FAQ - Azure VM의 SAP HANA 데이터베이스 백업
description: 이 문서에서는 Azure Backup 서비스를 사용하여 SAP HANA 데이터베이스를 백업하는 방법에 대한 일반적인 질문과 답변을 제공합니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: bf662600bafcd18b00c8f8d3b673fc3f9c110aca
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95400210"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>자주 묻는 질문 – Azure VM의 SAP HANA 데이터베이스 백업

이 문서에서는 Azure Backup 서비스를 사용하여 SAP HANA 데이터베이스를 백업하는 방법에 대한 일반적인 질문과 답변을 제공합니다.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>하루에 지원되는 전체 백업은 몇 건인가요?

하루에 한 건의 전체 백업만 지원됩니다. 차등 백업 및 전체 백업은 같은 날짜에 트리거될 수 없습니다.

### <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다. 포털 경고의 상세한 동작은 [여기](./backup-azure-monitoring-built-in-monitor.md)에서 확인하세요. 그러나 성공한 작업에 대 한 경고를 포함 하는 경우에는 [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md)를 사용할 수 있습니다.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>백업 작업 메뉴에서 예약된 백업 작업을 볼 수 있나요?

백업 작업 메뉴에는 주문형 백업 작업만 표시 됩니다. 예약된 작업을 보려면 [Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md)를 사용하세요.

### <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?

아니요, 현재 지원 되지 않습니다.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>인스턴스에서 데이터베이스를 삭제하면 백업은 어떻게 되나요?

SAP HANA 인스턴스에서 데이터베이스를 삭제해도 해당 데이터베이스의 백업이 계속 시도됩니다. 즉, 삭제된 데이터베이스가 **백업 항목** 에서 비정상 상태로 표시되기 시작하지만 여전히 보호되는 것으로 처리됩니다.
이 데이터베이스의 보호를 중지하는 올바른 방법은 이 데이터베이스에 대해 **데이터 삭제 시 백업 중지** 를 수행하는 것입니다.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>보호된 데이터베이스의 이름을 변경하면 어떻게 되나요?

이름이 변경된 데이터베이스는 새 데이터베이스로 처리됩니다. 따라서 서비스는 데이터베이스를 찾을 수 없어서 백업이 실패 하는 것 처럼이 상황을 처리 합니다. 이름이 변경된 데이터베이스는 새 데이터베이스로 표시되며 보호받도록 구성되어야 합니다.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>SAP HANA 데이터베이스를 Azure VM에 백업하기 위한 전제 조건은 무엇인가요?

[전제 조건](tutorial-backup-sap-hana-db.md#prerequisites) 및 [사전 등록 스크립트의 기능](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 섹션을 참조하세요.

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Azure에서 SAP HANA 데이터베이스를 백업할 수 있도록 설정 해야 하는 사용 권한은 무엇 인가요?

사전 등록 스크립트를 실행하면 Azure에서 SAP HANA 데이터베이스 백업을 허용하는 데 필요한 권한이 설정됩니다. 사전 등록 스크립트의 용도에 대 한 자세한 내용을 확인할 수 [있습니다.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>SAP HANA를 SDC에서 MDC로 마이그레이션한 후에도 백업이 작동하나요?

문제 해결 가이드의 [이 섹션](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid)을 참조하세요.

### <a name="what-should-be-done-while-upgrading-within-the-same-version"></a>동일한 버전 내에서 업그레이드 하는 동안 수행 해야 하는 작업은 무엇 인가요?

문제 해결 가이드에서 [이 섹션](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) 을 참조 하세요.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>가상 머신이 아닌 가상 IP(부하 분산 장치)에 대해 Azure HANA 백업을 설정할 수 있나요?

현재는 가상 IP에 대해서만 솔루션을 설정 하는 기능이 없습니다. 솔루션을 실행하려면 가상 머신이 필요합니다.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>주문형 백업을 Azure 자격 증명 모음이 아닌 로컬 파일 시스템으로 이동할 수 있나요?

1. 원하는 데이터베이스에서 현재 실행 중인 백업이 완료 될 때까지 기다립니다 (studio에서 완료를 확인).
1. 다음 단계에 따라 원하는 DB에 대해 로그 백업을 사용하지 않도록 설정하고 카탈로그 백업을 **파일 시스템** 으로 설정합니다.
1. **SYSTEMDB** -> **구성** -> **데이터베이스 선택** -> **필터(로그)** 를 두 번 클릭합니다.
    1. Enable_auto_log_backup를 **아니요** 로 설정 합니다.
    1. Catalog_backup_using_backint을 **false** 로 설정 합니다.
1. 원하는 데이터베이스에서 주문형 백업 (전체/차등/증분)을 수행 하 고 백업 및 카탈로그 백업이 완료 될 때까지 기다립니다.
1. 로그 백업을 Filesystem로 이동 하려면 enable_auto_log_backup을 **예** 로 설정 합니다.
1. 백업이 Azure 자격 증명 모음으로 흐를 수 있도록 이전 설정으로 되돌립니다.
    1. Enable_auto_log_backup를 **예** 로 설정 합니다.
    1. Catalog_backup_using_backint를 **true** 로 설정 합니다.

>[!NOTE]
>백업을 로컬 파일 시스템으로 이동 하 고 다시 Azure vault로 다시 전환 하면 자격 증명 모음에서 로그 백업의 로그 체인이 끊어질 수 있습니다. 이렇게 하면 전체 백업이 트리거되고 성공적으로 완료 되 면 로그 백업이 시작 됩니다.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>내 HANA 복제 설정으로 SAP HANA 백업을 사용 하려면 어떻게 하나요?

현재 Azure Backup에는 HSR 설정을 이해할 수 있는 기능이 없습니다. 즉, HSR의 기본 및 보조 노드는 관련이 없는 두 개의 개별 Vm으로 처리 됩니다. 먼저 주 노드에서 백업을 구성 해야 합니다. 장애 조치 (failover)가 발생 하면 보조 노드 (이제 주 노드가 됨)에 백업을 구성 해야 합니다. 다른 노드에 대 한 백업 자동 장애 조치 (failover)는 수행 되지 않습니다.

지정 된 시점에 활성 (주) 노드의 데이터를 백업 하려면 장애 조치 (failover) 후 주 노드로 **보호를 전환할** 수 있습니다.

이 **스위치 보호** 를 수행 하려면 다음 단계를 수행 합니다.

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

대상 SAP HANA 인스턴스로 복원하기 위한 모든 전제 조건이 충족되었는지 확인하세요. 자세한 내용은 [전제 조건 - Azure VM에서 SAP HANA 데이터베이스 복원](./sap-hana-db-restore.md#prerequisites)을 참조하세요.

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>데이터베이스에 대한 덮어쓰기 DB 복원이 실패하는 이유는 무엇인가요?

복원하는 동안 **무조건 덮어쓰기** 옵션이 선택되어 있는지 확인하세요.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>“복원의 원본 및 대상 시스템이 호환되지 않습니다” 오류가 표시되는 이유는 무엇인가요?

현재 지원되는 복원 유형은 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148)을 참조하세요.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>SLES에서 실행 되는 데이터베이스의 백업을 사용 하 여 RHEL HANA 시스템으로 복원 하거나 그 반대로 사용할 수 있나요?

예, SLES에서 실행 되는 HANA 데이터베이스에서 트리거되는 스트리밍 백업을 사용 하 여 RHEL HANA 시스템으로 복원 하 고 그 반대의 경우도 마찬가지입니다. 즉, 스트리밍 백업을 사용 하 여 OS 간 복원을 수행할 수 있습니다. 그러나 복원 하려는 HANA 시스템 및 복원에 사용 되는 HANA 시스템이 SAP에 따라 복원에 대해 모두 호환 되는지 확인 해야 합니다. 호환 되는 복원 유형을 확인 하려면 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) 을 참조 하세요.

## <a name="policy"></a>정책

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>SAP HANA 백업에 대 한 새 정책을 만들 때 사용할 수 있는 다양 한 옵션

정책을 만들기 전에 RPO 및 RTO의 요구 사항과 관련 비용 영향을 명확 하 게 해야 합니다.

RPO (복구 지점-목표)는 사용자/고객에 게 허용 되는 데이터 손실 정도를 나타냅니다. 이는 로그 백업 빈도에 의해 결정 됩니다. 로그 백업은 더 자주 발생 하며 Azure Backup 서비스에서 지 원하는 최소값은 15 분입니다. 따라서 로그 백업 빈도는 15 분 이상이 될 수 있습니다.

RTO (복구-시간-목표)는 데이터 손실 시나리오 후 마지막으로 사용 가능한 시점으로 데이터를 복원 하는 속도를 나타냅니다. 이는 일반적으로 복원에 필요한 파일 수에 따라 달라 지는 HANA에서 사용 하는 복구 전략에 따라 다릅니다. 이는 비용에도 영향을 미치므로 다음 표는 모든 시나리오와 해당 의미를 이해 하는 데 도움이 됩니다.

|백업 정책  |RTO  |비용  |
|---------|---------|---------|
|일별 전체 + 로그     |   지정 시간 복원에 대 한 전체 복사본 + 필수 로그가 하나만 필요 하기 때문에 가장 빠릅니다.      |    전체 복사본이 매일 수행 되므로 보존 시간이 될 때까지 백 엔드에 더 많은 데이터가 누적 되므로 Costliest 옵션을 사용 합니다.   |
|주간 전체 + 일별 차등 + 로그     |   지정 시간 복원에 대 한 하나의 전체 복사 + 1 개의 차등 복사 + 로그를 요구 하기 때문에 위의 옵션 보다 느리지만 다음 옵션 보다 빠릅니다.      |    일별 차등은 일반적으로 full 보다 작고 전체 복사본은 일주일에 한 번만 사용 되므로 비용이 저렴 한 옵션      |
|주간 전체 + 일별 증분 + 로그     |  지정 시간 복구를 위해 하나의 전체 복사본 + ' n ' 증분 + 로그가 필요 하므로 가장 느립니다.       |     일별 증분은 차등 보다 작고 전체 복사본은 주별 으로만 사용 되기 때문에 가장 저렴 한 옵션    |

> [!NOTE]
> 위의 옵션은 가장 일반적 이지만 유일한 옵션은 아닙니다. 예를 들어 매주 전체 백업 하 고 주 + 로그를 두 번 이상 사용할 수 있습니다.

따라서 RPO 및 RTO 목적과 비용 고려 사항에 따라 정책 변형을 선택할 수 있습니다.

### <a name="impact-of-modifying-a-policy"></a>정책 수정의 영향

백업 항목의 정책을 정책 1 (P1)에서 정책 2 (P2)로 전환 하거나 정책 1 (P1)을 편집 하는 경우의 영향을 확인 하는 경우 몇 가지 원칙을 염두에 두어야 합니다.

- 모든 변경 내용도 소급 적용 됩니다. 최신 백업 정책은 이전에 수행 된 복구 시점에도 적용 됩니다. 예를 들어 일별 전체 보존이 30 일이 고 현재 활성 정책에 따라 10 개의 복구 지점이 생성 된 것으로 가정 합니다. 매일 전체 보존이 10 일로 변경 되 면 이전 시점의 만료 시간도 시작 시간 + 10 일으로 다시 계산 되 고 만료 된 경우 삭제 됩니다.
- 변경 범위에는 백업 날짜, 백업 유형 (보존 포함)도 포함 됩니다. 예: 매주 전체를 일요일에 매주 전체로 변경 하는 경우 일요일에 없는 이전 전체 모두 삭제 되도록 표시 됩니다.
- 자식이 활성/만료 되지 않을 때까지 부모는 삭제 되지 않습니다. 모든 백업 유형에는 현재 활성 정책에 따라 만료 시간이 있습니다. 그러나 전체 백업 유형은 후속 ' 차등 ', ' 증분 ' 및 ' 로그 '에 대 한 부모로 간주 됩니다. ' 차등 ' 및 ' 로그 '는 다른 사람에 게 부모가 되지 않습니다. ' 증분 '은 후속 ' 증분 '의 부모일 수 있습니다. ' 부모 '가 삭제 되도록 표시 된 경우에도 자식 ' 차등 ' 또는 ' 로그 '가 만료 되지 않으면 실제로 삭제 되지 않습니다. 예를 들어, 일요일에 매주 전체에서 매주 전체로 정책을 변경 하면 일요일에 없는 이전 전체 모두 삭제 되도록 표시 됩니다. 그러나 이전에 수행 된 로그가 만료 될 때까지 실제로 삭제 되지 않습니다. 즉, 최신 로그 기간에 따라 유지 됩니다. 로그가 만료 되 면 로그와 이러한 전체 모두 삭제 됩니다.

이러한 원칙을 사용 하 여 다음 표를 읽고 정책 변경의 영향을 이해할 수 있습니다.

|이전 정책/새 정책  |일별 전체 + 로그  | 주간 전체 + 일별 차등 + 로그  |주간 전체 + 일별 증분 + 로그  |
|---------|---------|---------|---------|
|일별 전체 + 로그     |   -      |    같은 요일에 있지 않은 이전 전체는 삭제 하도록 표시 되지만 로그 보존 기간이 끝날 때까지 유지 됩니다.     |    같은 요일에 있지 않은 이전 전체는 삭제 하도록 표시 되지만 로그 보존 기간이 끝날 때까지 유지 됩니다.     |
|주간 전체 + 일별 차등 + 로그     |   이전 주간 전체 보존은 최신 정책에 따라 다시 계산 됩니다. 이전 차등은 즉시 삭제 됩니다.      |    -     |    이전 차등은 즉시 삭제 됩니다.     |
|주간 전체 + 일별 증분 + 로그     |     이전 주간 전체 보존은 최신 정책에 따라 다시 계산 됩니다. 이전 증분은 즉시 삭제 됩니다.    |     이전 증분은 즉시 삭제 됩니다.    |    -     |

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행되는 [SAP HANA 데이터베이스를 백업](./backup-azure-sap-hana-database.md)하는 방법을 알아봅니다.
