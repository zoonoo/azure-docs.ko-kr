---
title: "Azure SQL Database를 BACPAC 파일로 내보내기 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure SQL Database를 BACPAC 파일로 내보내기"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 06183c5e1b61c47a7229c2abcc64217ee57c2bac
ms.contentlocale: ko-kr
ms.lasthandoff: 04/27/2017


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Azure SQL Database를 BACPAC 파일로 내보내기

이 문서에서는 새 Azure SQL Database를 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일로 내보내는 방법을 살펴봅니다. 이 문서에서는 다음 방법에 대해 설명합니다.
- [Azure Portal](https://portal.azure.com)
- [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티
- [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) cmdlet
- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)의 [데이터 계층 응용 프로그램 내보내기](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) 마법사

> [!IMPORTANT] 
> Azure SQL Database 자동화된 내보내기는 현재 미리 보기이며 및 2017년 3월 1일에 사용이 중지됩니다. 2016년 12월 1일을 시작으로 모든 SQL Database에서 자동화된 내보내기를 더는 구성할 수 없습니다. 기존의 모든 자동화된 내보내기 작업은 2017년 3월 1일까지 계속 수행됩니다. 2016년 12월 1일 후에는 [장기 백업 보존](sql-database-long-term-retention.md
) 또는 [Azure Automation](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md)을 사용하여 선택한 일정에 따라 주기적으로 PowerShell을 사용해 주기적으로 SQL Database를 보관할 수 있습니다. 샘플 스크립트의 경우 GitHub에서 [샘플 PowerShell 스크립트](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)를 다운로드할 수 있습니다.
>

> [!IMPORTANT] 
> Azure SQL Database 자동화된 내보내기는 현재 미리 보기이며 및 2017년 3월 1일에 사용이 중지됩니다. 2016년 12월 1일을 시작으로 모든 SQL Database에서 자동화된 내보내기를 더는 구성할 수 없습니다. 기존의 모든 자동화된 내보내기 작업은 2017년 3월 1일까지 계속 수행됩니다. 2016년 12월 1일 후에는 [장기 백업 보존](sql-database-long-term-retention.md
) 또는 [Azure Automation](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md)을 사용하여 선택한 일정에 따라 주기적으로 PowerShell을 사용해 주기적으로 SQL Database를 보관할 수 있습니다. 샘플 스크립트의 경우 GitHub에서 [샘플 PowerShell 스크립트](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)를 다운로드할 수 있습니다.
>

## <a name="overview"></a>개요

다른 플랫폼에 보관하거나 이동하기 위해 데이터베이스를 내보내야 할 경우 데이터베이스 스키마 및 데이터를 BACPAC 파일로 내보낼 수 있습니다. BACPAC 파일은 메타데이터 및 SQL Server Database의 데이터를 포함하는 BACPAC의 확장명을 가진 ZIP 파일입니다. BACPAC 파일은 Azure Blob Storage 또는 온-프레미스 저장소의 로컬 저장소에 저장할 수 있으며 나중에 Azure SQL Database 또는 SQL Server 온-프레미스 설치로 다시 가져올 수 있습니다. 

> [!IMPORTANT]
> 마이그레이션에 대한 사전 준비로 SQL Server에서 Azure SQL Database로 내보내는 경우 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
> 

## <a name="considerations"></a>고려 사항

* 내보내기 작업에서 트랜잭션이 일치하도록 내보내기 중에나 Azure SQL Database의 [트랜잭션 일치 복사본](sql-database-copy.md)에서 내보내는 중에는 쓰기 활동이 발생하지 않도록 해야 합니다.
* Blob Storage로 내보내는 경우 BACPAC 파일의 최대 크기는 200GB입니다. 더 큰 BACPAC 파일을 보관하려면 로컬 저장소로 내보냅니다.
* 이 문서에서 설명하는 방법을 사용하여 Azure Premium Storage에서 BACPAC 파일을 내보낼 수는 없습니다.
* Azure SQL Database에서 내보내기 작업이 20시간을 초과하면 취소될 수 있습니다. 내보내는 중에 성능을 향상시키기 위해 다음을 수행할 수 있습니다.
  * 서비스 수준을 일시적으로 높이기
  * 내보내기 중에 모든 읽기 및 쓰기 작업 중단
  * 모든 대형 테이블에 null이 아닌 값의 [클러스터형 인덱스](https://msdn.microsoft.com/library/ms190457.aspx) 를 사용합니다. 클러스터형 인덱스가 없는 경우 6~12시간 이상 소요되면 내보내기에 실패할 수 있습니다. 전체 테이블 내보내기를 시도하려면 내보내기 서비스에서 테이블 스캔을 완료해야 하기 때문입니다. 테이블이 내보내기에 최적화되었는지 확인하는 좋은 방법은 **DBCC SHOW_STATISTICS**를 실행하고 *RANGE_HI_KEY*가 null이 아닌지와 해당 값이 잘 배포되어 있는지 검토하는 것입니다. 자세한 내용은 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)를 참조하세요.

> [!NOTE]
> BACPAC는 백업에 사용되는 목적이 아니며 작업을 복원합니다. Azure SQL 데이터베이스에서는 모든 사용자 데이터베이스의 백업이 자동으로 생성됩니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md) 및 [SQL Database 백업](sql-database-automated-backups.md)을 참조하세요.  
> 

## <a name="azure-portal"></a>Azure 포털

Azure Portal을 사용하여 내보내려면 해당 데이터베이스의 페이지를 열고 도구 모음에서 **내보내기**를 클릭합니다. *.bacpac 파일 이름을 지정하고, 내보내기를 위한 Azure Storage 계정 및 컨테이너를 제공하고, 자격 증명을 입력하여 원본 데이터베이스에 연결합니다.  

   ![데이터베이스 내보내기](./media/sql-database-export/database-export.png)

내보내기 작업의 진행률을 모니터링하려면 내보낼 데이터베이스가 포함된 논리 서버에 대한 페이지를 엽니다. **작업**이 나올 때까지 아래로 스크롤한 다음 **가져오기/내보내기** 기록을 클릭합니다.

## <a name="sqlpackage-utility"></a>SQLPackage 유틸리티

[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 SQL Database를 내보내려면 [매개 변수 및 속성 내보내기](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties)를 참조하세요. SQLPackage 유틸리티는 최신 버전의 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 [Visual Studio용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)가 함께 제공되며, Microsoft 다운로드 센터에서 직접 최신 버전의 [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876)를 다운로드할 수 있습니다.

대부분의 프로덕션 환경에서 규모 및 성능에 SQLPackage 유틸리티를 사용하는 것이 좋습니다. BACPAC 파일을 사용한 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.

이 예제는 Active Directory 유니버설 인증으로 SqlPackage.exe를 사용하여 데이터베이스를 내보내는 방법을 보여줍니다.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio"></a>SQL Server Management Studio

또한 최신 버전의 SQL Server Management Studio에서는 Azure SQL Database를 bacpac 파일로 내보내기 위한 마법사도 제공합니다. [데이터 계층 응용 프로그램 내보내기](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)를 참조하세요.

## <a name="powershell"></a>PowerShell

[New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) cmdlet을 사용하여 Azure SQL Database 서비스에 데이터베이스 내보내기 요청을 제출합니다. 데이터베이스 크기에 따라 내보내기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

내보내기 요청의 상태를 확인하려면 [Get AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) cmdlet을 사용합니다. 이 요청 직후에 이 명령을 실행하면 **Status: InProgress**가 반환됩니다. **Status : Succeeded**가 표시되면 내보내기가 완료된 것입니다.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

### <a name="export-sql-database-example"></a>SQL 데이터베이스 내보내기 예제
다음 예제에서는 기존 Azure SQL Database를 BACPAC에 내보내고 내보내기 작업의 상태를 확인하는 방법을 보여 줍니다.

이 예제를 실행하기 위해 데이터베이스 및 저장소 계정에 대한 특정 값으로 바꿔야 하는 몇 개의 변수가 있습니다. [Azure 포털](https://portal.azure.com)에서 저장소 계정으로 이동하여 저장소 계정 이름, Blob 컨테이너 이름 및 키 값을 가져옵니다. 저장소 계정 블레이드에서 **액세스 키** 를 클릭하여 키를 찾을 수 있습니다.

다음 `VARIABLE-VALUES` 를 특정 Azure 리소스에 대한 값으로 바꿉니다. 데이터베이스 이름은 내보낼 기존 데이터베이스입니다.

```powershell
$subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

# Database to export
$DatabaseName = "DATABASE-NAME"
$ResourceGroupName = "RESOURCE-GROUP-NAME"
$ServerName = "SERVER-NAME
$serverAdmin = "ADMIN-NAME"
$serverPassword = "ADMIN-PASSWORD" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

# Generate a unique filename for the BACPAC
$bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

# Storage account info for the BACPAC
$BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
$BacpacUri = $BaseStorageUri + $bacpacFilename
$StorageKeytype = "StorageAccessKey"
$StorageKey = "YOUR STORAGE KEY"

$exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password

$exportRequest

# Check status of the export
Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
```

## <a name="next-steps"></a>다음 단계

* 보관을 위해 데이터베이스를 내보내는 방법의 대안으로 사용되는 Azure SQL Database 백업의 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.
- BACPAC 파일을 사용하는 마이그레이션에 관한 SQL Server 고객 자문 팀 블로그는 [BACPAC 파일을 사용하여 SQL Server에서 Azure SQL Database로 마이그레이션](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)을 참조하세요.
* SQL Server Database에 BACPAC를 가져오는 방법에 대해 자세히 알아보려면 [SQL Server Database로 BACPCAC 가져오기](https://msdn.microsoft.com/library/hh710052.aspx)를 참조하세요.
* SQL Server Database에서 BACPAC를 내보내는 방법을 알아보려면 [데이터 계층 응용 프로그램 내보내기](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) 및 [첫 번째 데이터베이스 마이그레이션](sql-database-migrate-your-sql-server-database.md)을 참조하세요.

