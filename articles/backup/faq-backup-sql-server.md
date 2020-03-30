---
title: FAQ - Azure VM에서 SQL 서버 데이터베이스 백업
description: Azure 백업을 사용하여 Azure VM에서 SQL Server 데이터베이스 백업에 대한 일반적인 질문에 대한 답변을 찾습니다.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247710"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Azure VM 백업에서 실행 중인 SQL Server 데이터베이스에 대한 FAQ

이 문서에서는 Azure 가상 시스템(VM)에서 실행되고 [Azure 백업](backup-overview.md) 서비스를 사용하는 SQL Server 데이터베이스 백업에 대한 일반적인 질문에 대해 답변합니다.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>동일한 컴퓨터에서 IaaS VM및 SQL Server에 Azure 백업을 사용할 수 있습니까?

예. 동일한 VM에서 VM 백업과 SQL 백업을 모두 사용할 수 있습니다. 이 경우 내부적으로 로그를 트러버리지 않도록 VM에서 복사 전용 전체 백업을 트리거합니다.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>솔루션이 백업을 다시 시도하거나 자동으로 복구합니까?

경우에 따라 Azure Backup 서비스는 수정 백업을 트리거합니다. 자동 치유는 아래에 언급 된 여섯 가지 조건 중 어느 것에 대해서나 발생할 수 있습니다.

- LSN 유효성 검사 오류로 인해 로그 또는 차등 백업이 실패하면 다음 로그 또는 차등 백업이 전체 백업으로 변환됩니다.
- 로그 또는 차등 백업 전에 전체 백업이 발생하지 않은 경우 해당 로그 또는 차등 백업이 전체 백업으로 변환됩니다.
- 최신 전체 백업의 시점 이 15일이 지난 경우 다음 로그 또는 차등 백업이 전체 백업으로 변환됩니다.
- 확장 업그레이드로 인해 취소되는 모든 백업 작업은 업그레이드가 완료되고 확장이 시작된 후 다시 트리거됩니다.
- 복원 중에 데이터베이스를 덮어쓰도록 선택하면 다음 로그/차등 백업이 실패하고 대신 전체 백업이 트리거됩니다.
- 데이터베이스 복구 모델의 변경으로 인해 로그 체인을 재설정하기 위해 전체 백업이 필요한 경우 다음 일정에 따라 전체 백업이 자동으로 트리거됩니다.

기능으로 자동 치유는 기본적으로 모든 사용자에 대해 활성화됩니다. 그러나 옵트아웃을 선택한 경우 다음을 수행하십시오.

- SQL Server 인스턴스에서 *C:\프로그램 파일\Azure 워크로드 백업\빈* 폴더에서 **ExtensionSettingsOverrides.json** 파일을 만들거나 편집합니다.
- 확장 **설정Overrides.json에서** *{"인에이블오토힐러": false}*.
- 변경 내용을 저장하고 파일을 닫습니다.
- SQL Server 인스턴스에서 **작업 관리를** 연 다음 **AzureWLBackupCoordinatorSvc** 서비스를 다시 시작합니다.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>SQL 서버에서 실행되는 동시 백업 수를 제어할 수 있습니까?

예. SQL Server 인스턴스에 대한 영향을 최소화하기 위해 백업 정책이 실행되는 속도를 제한할 수 있습니다. 설정을 변경하려면:

1. SQL Server 인스턴스에서 *C:\프로그램 파일\Azure 워크로드 백업\빈* 폴더에서 *ExtensionSettingsOverrides.json* 파일을 만듭니다.
2. 확장 *설정Overrides.json* 파일에서 **기본 백업 작업임계값** 설정을 더 낮은 값(예: 5)으로 변경합니다. <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
기본 백업작업임계값의 기본값은 **20입니다.**

3. 변경 내용을 저장하고 파일을 닫습니다.
4. SQL Server 인스턴스에서 **작업 관리자**를 엽니다. **AzureWLBackupCoordinatorSvc** 서비스를 다시 시작합니다.<br/> <br/>
 이 메서드는 백업 응용 프로그램이 대량의 리소스를 사용하는 경우 도움이 되지만 SQL Server [Resource Governor는](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) 들어오는 응용 프로그램 요청에서 사용할 수 있는 CPU, 물리적 IO 및 메모리의 양에 대한 제한을 지정하는 보다 일반적인 방법입니다.

> [!NOTE]
> UX에서는 여전히 진행하여 주어진 시간에 많은 백업을 예약 할 수 있지만 위의 예에 따라 5라고 하는 슬라이딩 창에서 처리됩니다.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>보조 복제본에서 전체 백업을 실행할 수 있나요?

SQL 제한 사항에 따라 보조 복제본에서 전체 백업만 복사를 실행할 수 있습니다. 그러나 전체 백업은 허용되지 않습니다.

## <a name="can-i-protect-availability-groups-on-premises"></a>온-프레미스에서 가용성 그룹을 보호할 수 있습니까?

아니요. Azure 백업은 Azure에서 실행되는 SQL Server 데이터베이스를 보호합니다. 가용성 그룹(AG)이 Azure 및 온-프레미스 컴퓨터 간에 분산되어 있는 경우 기본 복제본이 Azure에서 실행 중인 경우에만 AG를 보호할 수 있습니다. 또한 Azure Backup은 복구 서비스 자격 증명 모음과 동일한 Azure 지역에서 실행되는 노드만 보호합니다.

## <a name="can-i-protect-availability-groups-across-regions"></a>Azure 지역 간에 가용성 그룹을 보호할 수 있나요?

Azure 백업 복구 서비스 자격 증명 모음은 볼트와 동일한 지역에 있는 모든 노드를 검색하고 보호할 수 있습니다. SQL Server Always On 가용성 그룹이 여러 Azure 지역에 걸쳐 있는 경우 기본 노드가 있는 리전에서 백업을 설정합니다. 그러면 Azure Backup이 백업 기본 설정에 따라 가용성 그룹의 모든 데이터베이스를 검색하고 보호할 수 있습니다. 백업 기본 설정이 충족되지 않으면 백업이 실패하고 실패 경고가 표시됩니다.

## <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다. 포털 경고에 대한 자세한 동작은 [여기에](backup-azure-monitoring-built-in-monitor.md)설명되어 있습니다. 그러나 관심이 있는 경우 성공적인 작업에 대해서도 경고가 있는 경우 Azure Monitor 를 [사용하여 모니터링을](backup-azure-monitoring-use-azuremonitor.md)사용할 수 있습니다.

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>백업 작업 메뉴에서 예약된 백업 작업을 볼 수 있습니까?

**백업 작업** 메뉴에는 주문형 백업 작업만 표시됩니다. 예약된 작업 [사용의 경우 Azure 모니터를 사용하여 모니터링을](backup-azure-monitoring-use-azuremonitor.md)사용합니다.

## <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?

예. [자동 보호를](backup-sql-server-database-azure-vms.md#enable-auto-protection)통해 이 기능을 수행할 수 있습니다.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>자동 보호 인스턴스에서 데이터베이스를 삭제하면 백업은 어떻게 됩니까?

자동 보호 인스턴스에서 데이터베이스가 삭제된 경우에도 데이터베이스 백업은 계속 시도됩니다. 즉, 삭제된 데이터베이스가 **백업 항목** 아래에 비정상으로 나타나기 시작하고 여전히 보호됨을 의미합니다.

이 데이터베이스 보호를 중지하는 올바른 방법은 이 데이터베이스의 **데이터를 삭제하여** **백업 중지를** 수행하는 것입니다.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>자동 보호 된 데이터베이스의 백업 작업을 중지하면 그 동작은 무엇입니까?

**유지 데이터로 백업을 중지하면**향후 백업이 수행되지 않으며 기존 복구 지점은 그대로 유지됩니다. 데이터베이스는 여전히 보호된 것으로 간주되며 **백업 항목**아래에 표시됩니다.

**삭제 데이터로 백업을 중지하면**향후 백업이 수행되지 않으며 기존 복구 지점도 삭제됩니다. 데이터베이스는 보호되지 않은 것으로 간주되며 백업 구성의 인스턴스 아래에 표시됩니다. 그러나 수동으로 선택하거나 자동 보호를 받을 수 있는 다른 업보호 데이터베이스와 달리 이 데이터베이스는 회색으로 표시되어 선택할 수 없습니다. 이 데이터베이스를 다시 보호하는 유일한 방법은 인스턴스에서 자동 보호를 사용하지 않도록 설정하는 것입니다. 이제 이 데이터베이스를 선택하고 이 데이터베이스에서 보호를 구성하거나 인스턴스에서 자동 보호를 다시 활성화할 수 있습니다.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>데이터베이스가 보호된 후 데이터베이스 이름을 변경하면 어떤 동작이 됩니까?

이름이 변경된 데이터베이스는 새 데이터베이스로 처리됩니다. 따라서 서비스는 이 상황을 데이터베이스가 찾을 수 없고 백업에 실패하는 것처럼 처리합니다.

이제 이름이 바뀌는 데이터베이스를 선택하고 보호를 구성할 수 있습니다. 인스턴스에서 자동 보호가 활성화된 경우 이름이 변경된 데이터베이스가 자동으로 검색되고 보호됩니다.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>자동 보호 인스턴스에 대해 추가된 데이터베이스를 볼 수 없는 이유는 무엇입니까?

자동 보호 [인스턴스에 추가하는](backup-sql-server-database-azure-vms.md#enable-auto-protection) 데이터베이스는 보호된 항목 아래에 즉시 나타나지 않을 수 있습니다. 일반적으로 검색이 8시간마다 실행되기 때문입니다. 그러나 다음 이미지와 같이 **다시 발견 DB를**선택하여 검색을 수동으로 실행하는 경우 새 데이터베이스를 즉시 검색하고 보호할 수 있습니다.

  ![새로 추가된 데이터베이스를 수동으로 검색합니다.](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행 중인 [SQL Server 데이터베이스를 백업하는](backup-azure-sql-database.md) 방법을 알아봅니다.
