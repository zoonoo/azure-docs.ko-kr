---
title: BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기 | Microsoft Docs
description: BACPAC 파일을 가져와 새 Azure SQL 데이터베이스를 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/05/2018
ms.openlocfilehash: 6753666f1747c95ad3486444ed41e3cad0b8e905
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084179"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>빠른 시작: 새 Azure SQL Database로 BACPAC 파일 가져오기

[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일(데이터베이스의 메타데이터와 데이터가 포함된 확장명이 `.bacpac`인 zip 파일)을 사용하여 SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션할 수 있습니다. BACPAC 파일은 Azure Blob Storage(표준 스토리지만 해당)에서 또는 온-프레미스 위치의 로컬 스토리지에서 가져올 수 있습니다. 가져오기 속도를 최대화하려면 높은 서비스 계층과 컴퓨팅 크기(예 P6)를 지정한 다음, 가져오기가 성공한 후에 규모를 축소하는 것이 좋습니다. 가져온 데이터베이스의 호환성 수준은 원본 데이터베이스의 호환성 수준에 기반합니다.

> [!IMPORTANT]
> 데이터베이스를 가져온 후에는 데이터베이스를 현재 호환성 수준에서(AdventureWorks2008R2 데이터베이스에 대해 수준 100) 또는 더 높은 수준에서 작동하도록 선택할 수 있습니다. 특정 호환성 수준에서 데이터베이스를 운영하기 위한 옵션 및 그 영향에 대한 자세한 내용은 [ALTER DATABASE 호환성 수준](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요. 또한 호환성 수준과 관련된 추가 데이터베이스 수준 설정에 대한 자세한 내용은 [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)을 참조하세요.

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Azure Portal의 BACPAC 파일에서 가져오기

이 섹션에서는 [Azure Portal](https://portal.azure.com)에서 Azure Blob Storage에 저장된 BACPAC 파일로부터 Azure SQL 데이터베이스를 만드는 방법을 보여줍니다. 포털은 Azure Blob Storage에서 BACPAC 파일을 가져오는 *것만* 지원합니다.

> [!NOTE]
> [Azure SQL Database Managed Instance](sql-database-managed-instance.md)는 이 문서의 다른 방법을 사용하여 BACPAC 파일에서 가져오기를 지원하지만, 현재는 Azure Portal에서 마이그레이션을 지원하지 않습니다.

Azure Portal에서 데이터베이스를 가져오려면 가져오기를 호스트할 논리 서버의 페이지를 열고 도구 모음에서 **데이터베이스 가져오기**를 선택합니다.  

   ![데이터베이스 가져오기](./media/sql-database-import/import.png)

가져올 스토리지 계정, 컨테이너 및 BACPAC 파일을 선택합니다. 새 데이터베이스의 크기(일반적으로 원본과 동일)를 지정하고 대상 SQL Server 자격 증명을 제공합니다. 

### <a name="monitor-imports-progress"></a>가져오기의 진행률 모니터링

가져오기 진행률을 모니터링하려면 가져온 데이터베이스의 논리 서버 페이지를 열고 **설정**에서 **가져오기/내보내기 기록**을 선택합니다. 성공하면 가져오기가 **완료됨** 상태가 됩니다.

데이터베이스가 서버에서 라이브 상태인지 확인하려면 **SQL 데이터베이스**를 선택하고 새 데이터베이스가 **온라인** 상태인지 확인합니다.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SqlPackage를 사용하여 BACPAC 파일에서 가져오기

[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) 명령줄 유틸리티를 사용하여 SQL 데이터베이스를 가져오려면 [매개 변수 및 속성 가져오기](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)를 참조하세요. SqlPackage는 최신 버전의 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx)에 제공됩니다. Microsoft 다운로드 센터에서 최신 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)를 다운로드할 수도 있습니다.

규모와 성능을 위해 대부분의 프로덕션 환경에서 SqlPackage를 사용하는 것이 좋습니다. BACPAC 파일을 사용하는 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.

다음 SqlPackage 명령은 로컬 스토리지의 **AdventureWorks2008R2** 데이터베이스를 **mynewserver20170403**이라는 Azure SQL Database 논리 서버로 가져옵니다. **프리미엄** 서비스 계층과 **P6** 서비스 목표로 **myMigratedDatabase**라는 새 데이터베이스를 만듭니다. 이러한 값을 사용자 환경에 적절하게 변경합니다.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Azure SQL Database 논리 서버는 포트 1433에서 수신 대기합니다. 회사 방화벽 뒤에서 논리 서버에 연결하려면 방화벽에서 이 포트가 열려 있어야 합니다.
>

이 예제는 Active Directory 유니버설 인증으로 SqlPackage를 사용하여 데이터베이스를 가져오는 방법을 보여줍니다.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>PowerShell을 사용하여 BACPAC 파일에서 가져오기

[New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) cmdlet을 사용하여 Azure SQL Database 서비스에 데이터베이스 가져오기 요청을 제출합니다. 데이터베이스 크기에 따라 가져오기를 완료하는 데 다소 시간이 걸릴 수 있습니다.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "myResourceGroup" `
    -ServerName "myLogicalServer" `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName "myStorageAccount").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet을 사용하여 가져오기의 진행 상황을 확인할 수 있습니다. 요청 직후에 cmdlet을 실행하면 대개 **상태: 진행 중**이 반환됩니다. **상태: 성공함**이 표시되면 가져오기가 완료된 것입니다.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
다른 스크립트 예제는 [BACPAC 파일에서 데이터베이스 가져오기](scripts/sql-database-import-from-bacpac-powershell.md)를 참조하세요.

## <a name="limitations"></a>제한 사항

탄력적 풀의 데이터베이스로 가져오기는 지원되지 않습니다. 단일 데이터베이스로 데이터를 가져온 다음, 해당 데이터베이스를 풀로 이동할 수 있습니다.

## <a name="import-using-wizards"></a>마법사를 사용하여 가져오기

다음 마법사를 사용할 수도 있습니다.

- [SQL Server Management Studio의 데이터 계층 응용 프로그램 가져오기 마법사](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)
- [SQL Server 가져오기 및 내보내기 마법사](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)

## <a name="next-steps"></a>다음 단계

- 가져온 SQL Database에 연결하고 쿼리하는 방법을 알아보려면 [빠른 시작: Azure SQL Database: SQL Server Management Studio를 사용하여 데이터에 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.
- BACPAC 파일을 사용하는 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.
- 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database로 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
- 저장소 키 및 공유 액세스 서명을 안전하게 관리하고 공유하는 방법을 알아보려면 [Azure Storage 보안 가이드](https://docs.microsoft.com/azure/storage/common/storage-security-guide)를 참조하세요.
