---
title: FAQ - Azure VM의 SAP HANA 데이터베이스 백업
description: 이 문서에서는 Azure Backup 서비스를 사용하여 SAP HANA 데이터베이스 백업에 대한 일반적인 질문에 대한 답변을 알아보십시오.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155395"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>자주 묻는 질문 – Azure VM에서 SAP HANA 데이터베이스 백업

이 문서에서는 Azure 백업 서비스를 사용하여 SAP HANA 데이터베이스 백업에 대한 일반적인 질문에 대한 답변을 답변합니다.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>하루에 얼마나 많은 전체 백업이 지원되나요?

우리는 하루에 하나의 전체 백업을 지원합니다. 같은 날 차등 백업 및 전체 백업을 트리거할 수 없습니다.

### <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다. 포털 경고에 대한 자세한 동작은 [여기에](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)설명되어 있습니다. 그러나 성공적인 작업에 대해서도 경고가 있는 경우 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)를 사용할 수 있습니다.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>백업 작업 메뉴에서 예약된 백업 작업을 볼 수 있습니까?

백업 작업 메뉴에는 임시 백업 작업만 표시됩니다. 예약된 작업의 경우 [Azure 모니터](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)를 사용합니다.

### <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?

아니요, 현재 지원되지 않습니다.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>인스턴스에서 데이터베이스를 삭제하면 백업은 어떻게 됩니까?

SAP HANA 인스턴스에서 데이터베이스가 삭제된 경우에도 데이터베이스 백업은 계속 시도됩니다. 즉, 삭제된 데이터베이스가 **백업 항목** 아래에 비정상으로 나타나기 시작하고 여전히 보호됨을 의미합니다.
이 데이터베이스 보호를 중지하는 올바른 방법은 이 데이터베이스의 **삭제 데이터와 함께 백업 중지를** 수행하는 것입니다.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>데이터베이스가 보호된 후 데이터베이스 이름을 변경하면 어떤 동작이 발생합니까?

이름이 변경된 데이터베이스는 새 데이터베이스로 처리됩니다. 따라서 서비스는 이 상황을 데이터베이스가 찾을 수 없고 백업에 실패하는 것처럼 처리합니다. 이름이 변경된 데이터베이스는 새 데이터베이스로 표시되며 보호를 위해 구성되어야 합니다.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Azure VM에서 SAP HANA 데이터베이스를 백업하기 위한 필수 구성 조건은 무엇입니까?

[필수 구성 조건](tutorial-backup-sap-hana-db.md#prerequisites) 및 [사전 등록 스크립트가 수행하는 작업을](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 참조하십시오.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Azure에서 SAP HANA 데이터베이스를 백업할 수 있도록 설정해야 하는 권한은 무엇입니까?

사전 등록 스크립트를 실행하면 Azure에서 SAP HANA 데이터베이스를 백업할 수 있도록 필요한 권한이 설정됩니다. 사전 등록 스크립트가 수행하는 내용은 여기에서 확인할 수 [있습니다.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>SAP HANA를 1.0에서 2.0으로 마이그레이션한 후 백업이 작동합니까?

문제 해결 가이드의 [이 섹션을](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) 참조하십시오.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Azure HANA 백업을 가상 컴퓨터가 아닌 가상 IP(로드 밸러버)에 대해 설정할 수 있습니까?

현재 우리는 가상 IP에 대해 솔루션을 설정할 수있는 기능이 없습니다. 솔루션을 실행하려면 가상 시스템이 필요합니다.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>SAP HANA 시스템 복제(HSR)가 있는 경우 이 설정에 대한 백업을 어떻게 구성해야 합니까?

HSR의 기본 노드와 보조 노드는 두 개의 개별, 비관련 VM으로 처리됩니다. 기본 노드에서 백업을 구성해야 하며 장애 조치(failover)가 발생하면 보조 노드에서 백업을 구성해야 합니다(이제 기본 노드가 됨). 다른 노드에 대한 백업의 자동 '장애 조치'는 없습니다.

## <a name="restore"></a>복원

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>데이터베이스를 복원하려는 HANA 시스템을 볼 수 없는 이유는 무엇입니까?

대상 SAP HANA 인스턴스에 대한 복원의 모든 필수 구성 조건이 충족되었는지 확인합니다. 자세한 내용은 [전제 조건 - Azure VM에서 SAP HANA 데이터베이스 복원을](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)참조하십시오.

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>내 데이터베이스에 대해 덮어쓰기 DB 복원이 실패하는 이유는 무엇입니까?

복원하는 동안 **강제 덮어쓰기** 옵션이 선택되어 있는지 확인합니다.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>"복원을 위한 소스 및 대상 시스템이 호환되지 않습니다" 오류가 표시되는 이유는 무엇입니까?

현재 지원되는 복원 유형을 보려면 SAP HANA Note [1642148을](https://launchpad.support.sap.com/#/notes/1642148) 참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행되는 [SAP HANA 데이터베이스를 백업하는](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) 방법에 대해 알아봅니다.
