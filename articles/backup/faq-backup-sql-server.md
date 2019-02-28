---
title: Azure Backup을 사용하여 Azure VM의 SQL Server 데이터베이스를 백업하는 방법에 대한 자주 묻는 질문
description: Azure Backup을 사용하여 Azure VM의 SQL Server 데이터베이스를 백업하는 방법에 대한 일반적인 질문과 대답을 제공합니다.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430927"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Azure VM 백업에서 실행 중인 SQL Server에 대한 FAQ

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 Azure VM에서 실행 중인 SQL Server 데이터베이스를 백업하는 방법에 대한 일반적인 질문과 대답을 제공합니다.

> [!NOTE]
> 이 기능은 현재 공개 미리 보기로 제공됩니다.



## <a name="can-i-throttle-the-backup-speed"></a>백업 속도를 제한할 수 있나요?

예. SQL Server 인스턴스에 대한 영향을 최소화하기 위해 백업 정책이 실행되는 속도를 제한할 수 있습니다. 설정을 변경하려면:
1. SQL Server 인스턴스의 *C:\Program Files\Azure Workload Backup\bin 폴더*에서 **ExtensionSettingsOverrides.json** 파일을 만듭니다.
2. **ExtensionSettingsOverrides.json** 파일에서 **DefaultBackupTasksThreshold** 설정을 더 낮은 값(예: 5)으로 변경합니다. <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. 변경 내용을 저장합니다. 파일을 닫습니다.
4. SQL Server 인스턴스에서 **작업 관리자**를 엽니다. **AzureWLBackupCoordinatorSvc** 서비스를 다시 시작합니다.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>보조 복제본에서 전체 백업을 실행할 수 있나요?
 아니요. 이 기능은 지원되지 않습니다.

## <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

 아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>작업 메뉴에서 예약된 백업 작업을 볼 수 있나요?

 아니요. **백업 작업** 메뉴에는 주문형 작업 정보만 표시되고 예약된 백업 작업은 표시되지 않습니다. 예약된 백업 작업이 실패하면 실패한 작업 경고에서 세부 정보가 제공됩니다. 예약된 작업과 임시 백업 작업을 모두 모니터링하려는 경우 [SQL Server Management Studio](manage-monitor-sql-database-backup.md)를 사용하세요.

## <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?

 아니요. SQL Server 인스턴스에 대한 보호를 구성할 때 서버 수준 옵션을 선택하면 모든 데이터베이스가 추가됩니다. 보호를 구성한 후 SQL Server 인스턴스에 데이터베이스를 추가하는 경우에는 보호할 새 데이터베이스를 수동으로 추가해야 합니다. 데이터베이스는 구성된 보호에 자동으로 포함되지 않습니다.

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>복구 유형을 변경한 후 보호를 다시 시작하려면 어떻게 할까요?

전체 백업을 트리거하세요. 로그 백업이 예상대로 시작됩니다.

## <a name="can-i-protect-availability-groups-on-premises"></a>온-프레미스에서 가용성 그룹을 보호할 수 있나요?

 아니요. Azure Backup은 Azure에서 실행하는 SQL Server만 보호합니다. AG(가용성 그룹)이 Azure와 온-프레미스 머신 간에 분산되어 있는 경우 주 복제본을 Azure에서 실행하는 경우에만 가용성 그룹이 보호될 수 있습니다. 또한 Azure Backup은 Recovery Services 자격 증명 모음과 동일한 Azure 지역에서 실행 중인 노드만 보호합니다.

## <a name="can-i-protect-availability-groups-across-regions"></a>지역 간에 가용성 그룹을 보호할 수 있나요?

Azure Backup Recovery Services Vault는 Recovery Services Vault와 같은 지역에 있는 모든 노드를 검색하고 보호할 수 있습니다. SQL AlwaysOn 가용성 그룹이 여러 Azure 지역에 분산되어 있는 경우에는 기본 노드가 있는 지역에서 백업을 구성해야 합니다. 그러면 Azure Backup이 백업 기본 설정에 따라 가용성 그룹의 모든 데이터베이스를 검색하고 보호할 수 있습니다. 백업 기본 설정이 충족되지 않으면 백업은 실패하며 오류 경고가 표시됩니다.

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>자동 보호가 사용되는 데이터베이스를 제외할 수 있나요?

아니요, [자동 보호](backup-azure-sql-database.md#enable-auto-protection)는 인스턴스 전체에 적용됩니다. 자동 보호를 사용하여 인스턴스의 데이터베이스를 선택적으로 보호할 수 없습니다.

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>자동 보호된 인스턴스에 여러 정책을 적용할 수 있나요?

인스턴스에 보호되는 데이터베이스가 이미 있는 경우 해당 데이터베이스는 [자동 보호](backup-azure-sql-database.md#enable-auto-protection) 옵션을 **ON**으로 설정하더라도 각각의 정책을 통해 계속 보호됩니다. 그러나 보호되지 않은 데이터베이스와 나중에 추가되는 데이터베이스에는 데이터베이스를 선택한 후 **백업 구성**에서 정의하는 단일 정책만 적용됩니다. 사실 다른 보호된 데이터베이스와는 다르게, 자동 보호 인스턴스의 데이터베이스에 대한 정책을 변경할 수도 없습니다.
변경하는 유일한 방법은 당분간 인스턴스에 자동 보호를 사용하지 않도록 설정한 다음, 해당 데이터베이스에 대한 정책을 변경하는 것입니다. 이제 이 인스턴스에 대한 자동 보호를 다시 설정할 수 있습니다.

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>자동 보호에서 데이터베이스를 삭제하면 백업이 중지되나요?

아니요, 자동 보호 인스턴스에서 데이터베이스를 삭제해도 해당 데이터베이스의 백업이 계속 시도됩니다. 즉, 삭제된 데이터베이스가 **백업 항목**에서 비정상 상태로 표시되기 시작하지만 여전히 보호되는 것으로 처리됩니다.

이 데이터베이스 보호를 중지하는 유일한 방법은 당분간 인스턴스에 [자동 보호](backup-azure-sql-database.md#enable-auto-protection)를 사용하지 않도록 설정한 다음, 해당 데이터베이스의 **백업 항목** 아래에서 **백업 중지**를 선택하는 것입니다. 이제 이 인스턴스에 대한 자동 보호를 다시 설정할 수 있습니다.

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>자동 보호된 인스턴스에 대해 추가된 데이터베이스가 표시되지 않는 이유는 무엇인가요?

[자동 보호된](backup-azure-sql-database.md#enable-auto-protection) 인스턴스에 새로 추가된 데이터베이스가 보호된 항목 아래에 즉시 표시되지 않을 수도 있습니다. 일반적으로 검색이 8시간마다 실행되기 때문입니다. 그러나 사용자는 아래 이미지처럼 **DB 복구** 옵션을 통해 수동 검색을 실행하여 즉시 새 데이터베이스를 찾아 보호할 수 있습니다.

  ![새로 추가된 데이터베이스 보기](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행 중인 SQL Server 데이터베이스를 백업하는 [방법을 알아봅니다](backup-azure-sql-database.md).
