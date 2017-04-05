---
title: "PowerShell: Azure SQL Database를 BACPAC 파일로 내보내기 | Microsoft Docs"
description: "PowerShell을 사용하여 Azure SQL Database를 BACPAC 파일로 내보내기"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 41774c7f1c038ee017d719e59ed79fb68999dac8
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>PowerShell을 사용하여 Azure SQL Database 또는 SQL Server 데이터베이스를 BACPAC 파일로 내보내기

이 문서에서는 PowerShell을 사용하여 Azure SQL Database 또는 SQL Server 데이터베이스를 Azure Blob Storage에 저장된 BACPAC 파일로 내보내기 위한 지침을 제공합니다. BACPAC 파일로 내보내기의 개요는 [BACPAC로 내보내기](sql-database-export.md)를 참조하세요.

> [!NOTE]
> [Azure Portal](sql-database-export-portal.md), [SQL Server Management Studio](sql-database-export-ssms.md) 또는 [SQLPackage](sql-database-export-sqlpackage.md)를 사용하여 Azure SQL Database 파일을 BACPAC 파일로 내보낼 수도 있습니다.
>

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* Azure 구독.
* Azure SQL 데이터베이스입니다.
* 표준 저장소에 BACPAC를 저장할 Blob 컨테이너가 있는 [Azure 표준 저장소 계정](../storage/storage-create-storage-account.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>데이터베이스 내보내기
[New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) cmdlet은 서비스에 내보내기 데이터베이스 요청을 제출합니다. 데이터베이스 크기에 따라 내보내기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

> [!IMPORTANT]
> BACPAC 파일의 트랜잭션 일관성을 위해 먼저 [데이터베이스의 사본을 만든 다음](scripts/sql-database-copy-database-to-new-server-powershell.md)데이터베이스 사본을 내보냅니다.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>내보내기 작업의 진행률 모니터링
[New-AzureRmSqlDatabaseExport](https://docs.microsoft.com//powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)를 실행한 후 [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus)를 실행하여 요청 상태를 확인할 수 있습니다. 이 요청 직후에 이 명령을 실행하면 **Status : InProgress**가 반환됩니다. **Status : Succeeded**가 표시되면 내보내기가 완료된 것입니다.

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>SQL 데이터베이스 내보내기 예제
다음 예제에서는 기존 SQL 데이터베이스를 BACPAC에 내보내고 내보내기 작업의 상태를 확인하는 방법을 보여 줍니다.

이 예제를 실행하기 위해 데이터베이스 및 저장소 계정에 대한 특정 값으로 바꿔야 하는 몇 개의 변수가 있습니다. [Azure 포털](https://portal.azure.com)에서 저장소 계정으로 이동하여 저장소 계정 이름, Blob 컨테이너 이름 및 키 값을 가져옵니다. 저장소 계정 블레이드에서 **액세스 키** 를 클릭하여 키를 찾을 수 있습니다.

다음 `VARIABLE-VALUES` 를 특정 Azure 리소스에 대한 값으로 바꿉니다. 데이터베이스 이름은 내보낼 기존 데이터베이스입니다.

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
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

## <a name="automate-export-using-azure-automation"></a>Azure Automation을 사용하여 내보내기 자동화

Azure SQL Database 자동화된 내보내기는 현재 미리 보기이며 및 2017년 3월 1일에 사용이 중지됩니다. 2016년 12월 1일을 시작으로 모든 SQL Database에서 자동화된 내보내기를 더는 구성할 수 없습니다. 기존의 모든 자동화된 내보내기 작업은 2017년 3월 1일까지 계속 수행됩니다. 2016년 12월 1일 후에는 [장기 백업 보존](sql-database-long-term-retention.md) 또는 [Azure Automation](../automation/automation-intro.md)을 사용하여 선택한 일정에 따라 주기적으로 PowerShell을 사용해 주기적으로 SQL Database를 보관할 수 있습니다. 샘플 스크립트의 경우, [GitHub에서 샘플 스크립트](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)를 다운로드할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
* Powershell을 사용하여 Azure SQL 데이터베이스를 가져오는 방법을 알아보려면 [PowerShell을 사용하여 BACPAC 가져오기](scripts/sql-database-import-from-bacpac-powershell.md)를 참조하세요.
* SQLPackage를 사용하여 BACPAC를 가져오는 방법에 대해 자세히 알아보려면 [SqlPackage를 사용하여 Azure SQL Database로 BACPCAC 가져오기](sql-database-import-sqlpackage.md)를 참조하세요.
* Azure Portal을 사용하여 BACPAC를 가져오는 방법에 대해 자세히 알아보려면 [Azure Portal을 사용하여 Azure SQL Database로 BACPCAC 가져오기](sql-database-import-portal.md)를 참조하세요.
* 성능 권장 사항을 비롯한 전체 SQL Server 데이터베이스 마이그레이션 프로세스에 대한 설명은 [Azure SQL Database에 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
* 보관을 위해 데이터베이스를 내보내는 방법의 대안으로 사용되는 Azure SQL Database 백업의 장기 백업 보존에 대해 알아보려면 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.
* SQL Server 데이터베이스에 BACPAC를 가져오는 방법에 대해 자세히 알아보려면 [SQL Server 데이터베이스로 BACPCAC 가져오기](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>추가 리소스
* [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus)


