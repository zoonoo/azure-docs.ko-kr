---
title: '빠른 시작: 백업 복원(SSMS)'
titleSuffix: Azure SQL Managed Instance
description: 이 빠른 시작에서는 SSMS(SQL Server Management Studio)를 사용하여 Azure SQL Managed Instance로 데이터베이스 백업을 복원하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 413786cf8946c1ffbb76bd0e18eae7c7ba16a9c1
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790749"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>빠른 시작: SSMS를 사용하여 Azure SQL Managed Instance로 데이터베이스 복원
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 빠른 시작에서는 SSMS(SQL Server Management Studio)를 사용하여 Azure Blob 스토리지에서 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)로 데이터베이스(Wide World Importers - Standard 백업 파일)를 복원합니다.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Azure Database Migration Service를 사용하여 마이그레이션하는 방법에 대한 자세한 내용은 [Database Migration Service를 사용한 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
> 다양한 마이그레이션 방법에 대한 자세한 내용은 [SQL Server를 Azure SQL Managed Instance로 마이그레이션](migrate-to-instance-from-sql-server.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작의 특징은 다음과 같습니다.

- [관리형 인스턴스 만들기](instance-create-quickstart.md) 빠른 시작의 리소스를 사용합니다.
- 최신 버전의 [SSMS](/sql/ssms/sql-server-management-studio-ssms)가 설치되어 있어야 합니다.
- SSMS를 사용하여 SQL Managed Instance에 연결해야 합니다. 다음과 같이 연결하는 방법에 대한 빠른 시작을 참조하세요.
  - SQL Managed Instance에서 [퍼블릭 엔드포인트 사용](public-endpoint-configure.md) - 이 자습서에서 권장하는 방법입니다.
  - [Azure VM에서 SQL Managed Instance에 연결](connect-vm-instance-configure.md)
  - [온-프레미스에서 SQL Managed Instance로의 지점 및 사이트 간 연결을 구성합니다](point-to-site-p2s-configure.md).

> [!NOTE]
> Azure Blob Storage와 [SAS(공유 액세스 서명) 키](../../storage/common/storage-sas-overview.md)를 사용하여 SQL Server Database를 백업하고 복원하는 데 관한 자세한 내용은 [URL에 SQL Server 백업](/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)을 참조하세요.

## <a name="restore-from-a-backup-file"></a>백업 파일에서 복원

SQL Server Management Studio에서 이러한 단계에 따라 SQL Managed Instance로 Wide World Importers 데이터베이스를 복원합니다. 데이터베이스 백업 파일은 미리 구성된 Azure Blob Storage 계정에 저장됩니다.

1. SSMS를 열고 관리형 인스턴스에 연결합니다.
2. **개체 탐색기** 에서 관리형 인스턴스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택하여 새 쿼리 창을 엽니다.
3. 미리 구성된 스토리지 계정과 SAS 키를 사용하여 관리형 인스턴스에 [자격 증명을 만드는](/sql/t-sql/statements/create-credential-transact-sql) 다음 SQL 스크립트를 실행합니다.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![자격 증명 만들기](./media/restore-sample-database-quickstart/credential.png)

4. 자격 증명을 확인하려면 [컨테이너](https://azure.microsoft.com/services/container-instances/) URL을 사용하여 백업 파일 목록을 가져오는 다음 스크립트를 실행합니다.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![파일 목록](./media/restore-sample-database-quickstart/file-list.png)

5. Wide World Importers 데이터베이스를 복원하려면 다음 스크립트를 실행합니다.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![스크린샷은 성공 메시지와 함께 개체 탐색기에서 실행되는 스크립트를 보여줍니다.](./media/restore-sample-database-quickstart/restore.png)

6. 다음 스크립트를 실행하여 복원 상태를 추적합니다.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. 복원이 완료되면 개체 탐색기에서 데이터베이스를 봅니다. [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 보기를 사용하여 데이터베이스 복원이 완료되었는지 확인할 수 있습니다.

> [!NOTE]
> 데이터베이스 복원 작업은 비동기로 진행되며 다시 시도할 수 있습니다. 연결이 끊어지거나 제한 시간이 만료되는 경우 SQL Server Management Studio에서 오류가 발생할 수 있습니다. Azure SQL Database는 백그라운드에서 데이터베이스 복원을 계속 시도하며, [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 및 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 보기를 사용하여 복원 진행률을 추적할 수 있습니다.
> 복원 프로세스의 일부 단계에서는 시스템 보기에 실제 데이터베이스 이름 대신 고유 식별자가 표시됩니다. [여기](./transact-sql-tsql-differences-sql-server.md#restore-statement)에서 `RESTORE` 문 동작 차이점에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

- 5단계에서 데이터베이스 복원이 종료되고 메시지 ID 22003이 표시되는 경우 백업 체크섬이 포함된 새 백업 파일을 만들고 복원을 다시 수행합니다. [백업 또는 복원 중 백업 체크섬 사용 또는 사용 안 함](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)을 참조하세요.
- URL에 대한 백업과 관련된 문제를 해결하려면 [URL에 대한 SQL Server 백업 모범 사례 및 문제 해결](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)을 참조하세요.
- 앱 연결 옵션의 개요는 [애플리케이션을 SQL Managed Instance에 연결](connect-application-instance.md)을 참조하세요.
- 즐겨찾는 도구 또는 언어를 사용하여 쿼리하려면 [빠른 시작: Azure SQL Database 연결 및 쿼리](../database/connect-query-content-reference-guide.md)를 참조하세요.