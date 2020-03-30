---
title: Azure VM에서 SQL 서버 B관리 및 모니터링
description: 이 문서에서는 Azure VM에서 실행 중인 SQL Server 데이터베이스를 관리하고 모니터링하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 4daf068e97a08d1a611ef64cb64569cacd5d7420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172152"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>백업한 SQL Server 데이터베이스 관리 및 모니터링

이 문서에서는 Azure 가상 시스템(VM)에서 실행되고 [Azure 백업](backup-overview.md) 서비스 에서 Azure 백업 복구 서비스 자격 증명 모음에 백업되는 SQL Server 데이터베이스를 관리하고 모니터링하는 일반적인 작업에 대해 설명합니다. 작업 및 경고를 모니터링하고, 데이터베이스 보호를 중지 및 다시 시작하고, 백업 작업을 실행하고, 백업에서 VM을 등록 취소하는 방법을 알아봅니다.

SQL Server 데이터베이스에 대한 백업을 아직 구성하지 않은 경우 [Azure VM에서 SQL Server 데이터베이스 백업을](backup-azure-sql-database.md) 참조하세요.

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>포털에서 수동 백업 작업 모니터링

Azure Backup은 **Backup 작업** 포털에서 수동으로 트리거된 모든 작업을 표시합니다. 이 포털에 표시되는 작업에는 데이터베이스 검색 및 등록, 백업 및 복원 작업이 포함됩니다.

![백업 작업 포털](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **백업 작업** 포털에는 예약된 백업 작업이 표시되지 않습니다. SQL Server Management Studio를 사용하면 다음 섹션에서 설명하는 대로 예약된 백업 작업을 모니터링할 수 있습니다.
>

모니터링 시나리오에 대한 자세한 내용은 [Azure 포털의 모니터링](backup-azure-monitoring-built-in-monitor.md) 및 [Azure 모니터를 사용한 모니터링으로](backup-azure-monitoring-use-azuremonitor.md)이동합니다.  

## <a name="view-backup-alerts"></a>백업 경고 보기

로그 백업은 15분마다 발생하기 때문에 백업 작업 모니터링이 번거로울 수 있습니다. Azure Backup은 전자 메일 경고를 전송하여 모니터링을 용이하게 합니다. 이메일 알림은 다음과 같습니다.

- 모든 백업 오류에 대해 트리거됩니다.
- 오류 코드로 데이터베이스 수준에서 통합됩니다.
- 데이터베이스의 첫 번째 백업 실패에 대해서만 전송됩니다.

데이터베이스 백업 경고를 모니터링하려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. 자격 증명 모음 대시보드에서 **경고 및 이벤트**를 선택합니다.

   ![경고 및 이벤트 선택](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. **경고 및 이벤트**에서 **Backup 경고**를 선택합니다.

   ![백업 경고 선택](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server 데이터베이스에 대한 보호 중지

다음과 같은 여러 가지 방법으로 SQL Server 데이터베이스 백업을 중지할 수 있습니다.

- 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
- 이후의 모든 백업 작업을 중지하고 복구 지점을 그대로 둡니다.

복구 지점을 그대로 두기로 선택하는 경우 다음 세부 정보를 염두에 두어야 합니다.

- 모든 복구 지점은 영구적으로 유지되고, 모든 정리는 데이터 보관을 통해 보호 중지에서 중지됩니다.
- 보호된 인스턴스와 사용 된 저장소에 대 한 요금이 청구 됩니다. 자세한 내용은 [Azure 백업 가격 책정을](https://azure.microsoft.com/pricing/details/backup/)참조하십시오.
- 백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 실패합니다.

데이터베이스에 대한 보호를 중지하려면:

1. 볼트 대시보드에서 **백업 항목을 선택합니다.**

2. **백업 관리 유형에서** **Azure VM에서 SQL을**선택합니다.

    ![Azure VM의 SQL 선택](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 보호를 중지하려는 데이터베이스를 선택합니다.

    ![보호를 중지할 데이터베이스 선택](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 데이터베이스 메뉴에서 **백업 중지**를 선택합니다.

    ![백업 중지 선택](./media/backup-azure-sql-database/stop-db-button.png)

5. 백업 **중지** 메뉴에서 데이터를 유지하거나 삭제할지 여부를 선택합니다. 원하는 경우 이유와 의견을 제공하십시오.

    ![백업 중지 메뉴에서 데이터 유지 또는 삭제](./media/backup-azure-sql-database/stop-backup-button.png)

6. **백업 중지를**선택합니다.

> [!NOTE]
>
>데이터 삭제 옵션에 대한 자세한 내용은 아래 FAQ를 참조하십시오.
>
>- [자동 보호 인스턴스에서 데이터베이스를 삭제하면 백업은 어떻게 됩니까?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [자동 보호 된 데이터베이스의 백업 작업을 중지하면 그 동작은 무엇입니까?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>SQL 데이터베이스에 대한 보호 재개

SQL 데이터베이스에 대한 보호를 중지하면 백업 **데이터 유지** 옵션을 선택하면 나중에 보호를 다시 시작할 수 있습니다. 백업 데이터를 유지하지 않으면 보호를 다시 시작할 수 없습니다.

SQL 데이터베이스에 대한 보호를 다시 시작하려면 다음을 수행하십시오.

1. 백업 항목을 열고 **백업 다시 시작을**선택합니다.

    ![백업 다시 시작을 선택하여 데이터베이스 보호 다시 시작](./media/backup-azure-sql-database/resume-backup-button.png)

2. **백업 정책** 메뉴에서 정책을 선택한 다음, **저장**을 클릭합니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

다음과 같은 다양한 유형의 주문형 백업을 실행할 수 있습니다.

- 전체 백업
- 복사 전용 전체 백업
- 차등 백업
- 로그 백업

복사 전용 전체 백업의 보존 기간을 지정해야 하지만 온디맨드 전체 백업의 보존 범위는 현재 시간에서 45일로 자동으로 설정됩니다.

자세한 내용은 [SQL Server 백업 유형을](backup-architecture.md#sql-server-backup-types)참조하십시오.

## <a name="unregister-a-sql-server-instance"></a>SQL Server 인스턴스 등록 취소

보호를 비활성화한 후 볼트를 삭제하기 전에 SQL Server 인스턴스등록을 취소합니다.

1. 볼트 대시보드에서 **관리**에서 **백업 인프라를**선택합니다.  

   ![백업 인프라 선택](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. **관리 서버**에서 **보호된 서버**를 선택합니다.

   ![보호된 서버 선택](./media/backup-azure-sql-database/protected-servers.png)

3. **보호된 서버**에서 등록을 취소할 서버를 선택합니다. 자격 증명 모음을 삭제하려면 모든 서버의 등록을 취소해야 합니다.

4. 보호된 서버를 마우스 오른쪽 단추로 클릭하고 **등록 취소를**선택합니다.

   ![삭제 선택](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>정책 수정

백업 빈도 또는 보존 범위를 변경하려면 정책을 수정합니다.

> [!NOTE]
> 보존 기간의 모든 변경 사항은 새 복구 지점 외에 모든 이전 복구 지점에 소급 적용됩니다.

볼트 대시보드에서**백업 정책** **관리로** > 이동하여 편집할 정책을 선택합니다.

  ![백업 정책 관리](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![백업 정책 수정](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

정책 수정은 관련된 모든 백업 항목에 영향을 미치고 해당 **구성 보호** 작업을 트리거합니다.

### <a name="inconsistent-policy"></a>일관되지 않은 정책

경우에 따라 수정 정책 작업으로 인해 일부 백업 항목에 대해 정책 버전이 **일치하지** 않는 경우가 있습니다. 이 문제는 수정 정책 작업이 트리거된 후 백업 항목에 대해 해당 **구성 보호** 작업이 실패할 때 발생합니다. 백업 항목 보기에서 다음과 같이 나타납니다.

  ![일관되지 않은 정책](./media/backup-azure-sql-database/inconsistent-policy.png)

영향을 받은 모든 항목에 대한 정책 버전을 한 번의 클릭으로 수정할 수 있습니다.

  ![일관되지 않은 정책 수정](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>SQL Server VM에서 확장 다시 등록

경우에 따라 VM의 워크로드 확장이 한 가지 이유로 영향을 받을 수 있습니다. 이러한 경우 VM에서 트리거된 모든 작업이 실패하기 시작합니다. 그런 다음 VM에서 확장을 다시 등록해야 할 수 있습니다. **작업을 다시 등록하면** 작업을 계속할 수 있도록 VM에 워크로드 백업 확장을 다시 설치합니다.

이 옵션은 주의해서 사용하십시오. 이미 정상 확장이 있는 VM에서 트리거되면 이 작업으로 인해 확장이 다시 시작됩니다. 이로 인해 진행 중인 모든 작업이 실패할 수 있습니다. 재등록 작업을 트리거하기 전에 하나 이상의 [증상을](backup-sql-server-azure-troubleshoot.md#re-registration-failures) 확인하십시오.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [SQL Server 데이터베이스의 백업 문제 해결을](backup-sql-server-azure-troubleshoot.md)참조하십시오.
