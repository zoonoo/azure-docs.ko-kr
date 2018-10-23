---
title: Azure SQL Database Managed Instance에 백업 복원 | Microsoft Docs
description: SSMS를 사용하여 Azure SQL Database Managed Instance에 데이터베이스 백업을 복원합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: fa9686e7f9ca7f14a51ea2b9c313dd69a2e40cec
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362257"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 데이터베이스 백업 복원

이 빠른 시작에서는 Wide World Importers - 표준 백업 파일을 사용하여 Azure Blob 저장소에 저장된 데이터베이스의 백업을 Managed Instance로 복원하는 방법을 설명합니다. 이 메서드는 가동 중지 시간이 필요합니다. 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

Azure DMS(Database Migration Service)를 사용하여 마이그레이션하는 방법에 대한 자습서는 [DMS를 사용하여 Managed Instance 마이그레이션](../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요. 다양한 마이그레이션 방법에 대한 설명은 [SQL Server 인스턴스를 Azure SQL Database Managed Instance로 마이그레이션](sql-database-managed-instance-migrate.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작의 특징은 다음과 같습니다.
- [Managed Instance 만들기](sql-database-managed-instance-get-started.md) 빠른 시작에서 만든 리소스를 시작 지점으로 사용합니다.
- 온-프레미스 클라이언트 컴퓨터에 최신 버전의 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)가 필요합니다.
- SQL Server Management Studio를 사용하여 Managed Instance에 연결해야 합니다. 연결 옵션에 대해서는 다음 빠른 시작을 참조하세요.
  - [Azure VM에서 Azure SQL Database Managed Instance에 연결](sql-database-managed-instance-configure-vm.md)
  - [지점 및 사이트 간 연결을 사용하여 온-프레미스에서 Azure SQL Database Managed Instance에 연결](sql-database-managed-instance-configure-p2s.md).
- Wide World Importers - 표준 백업 파일(https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak)에서 다운로드)을 포함하여 미리 구성된 Azure Blob 저장소 계정을 사용합니다.

> [!NOTE]
> Azure Blob 저장소와 SAS(공유 액세스 서명)를 사용하여 SQL Server 데이터베이스를 백업하고 복원하는 방법에 대한 자세한 내용은 [URL에 SQL Server 백업 복원](sql-database-managed-instance-get-started-restore.md)을 참조하세요.

## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>백업 파일에서 Wide World Importers 데이터베이스 복원

SSMS를 사용하면 다음 단계를 사용하여 백업 파일에서 Managed Instance로 Wide World Importers 데이터베이스를 복원합니다.

1. SSMS(SQL Server Management Studio)를 열고 Managed Instance에 연결합니다.
2. SSMS에서 새 쿼리 창을 엽니다.
3. 다음 스크립트를 사용하여 미리 구성된 저장소 계정과 SAS 키를 통해 Managed Instance에 자격 증명을 만듭니다.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![자격 증명 만들기](./media/sql-database-managed-instance-get-started-restore/credential.png)

    > [!NOTE]
    > 항상 생성된 SAS 키에서 앞에 나오는 **?** 를 제거해 주세요.
  
3. 다음 스크립트를 사용하여 SAS 자격 증명 및 백업 유효성을 검사합니다. 백업 파일이 있는 컨테이너에 대한 URL을 제공합니다.

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![파일 목록](./media/sql-database-managed-instance-get-started-restore/file-list.png)

4. 다음 스크립트를 사용하여 백업 파일에서 Wide World Importers 데이터베이스를 복원합니다. 백업 파일이 있는 컨테이너에 대한 URL을 제공합니다.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![복원](./media/sql-database-managed-instance-get-started-restore/restore.png)

5. 복원 상태를 추적하려면 새 쿼리 세션에서 다음 쿼리를 실행합니다.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

6. 복원이 완료되면 개체 탐색기에서 봅니다. 

## <a name="next-steps"></a>다음 단계

- URL에 대한 백업 복원과 관련된 문제를 해결하려면 [URL에 대한 SQL Server 백업 - 최상의 방법 및 문제 해결](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)을 참조하세요.
- 응용 프로그램 연결 옵션에 대한 개요는 [응용 프로그램을 Managed Instance에 연결](sql-database-managed-instance-connect-app.md)을 참조하세요.
- 즐겨찾는 도구 또는 언어 중 하나를 사용하여 쿼리하려면 [연결 및 쿼리](sql-database-connect-query.md)를 참조하세요.
