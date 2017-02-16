---
title: "PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관"
description: "PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: ebbb31eb9387d68afab7559a3827682ed2551d5a
ms.openlocfilehash: de0b000b56ea90caeb1e2aa9a0b8c87e25c7c237


---
# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

이 문서에서는 PowerShell을 사용하여 Azure Blob 저장소에 저장된 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일로 Azure SQL 데이터베이스를 보관하기 위한 지침을 제공합니다.

Azure SQL 데이터베이스의 보관 파일을 만들어야 하는 경우 데이터베이스 스키마 및 데이터를 BACPAC 파일로 내보낼 수 있습니다. BACPAC 파일은 확장명이 .bacpac인 단순한 ZIP 파일입니다. 나중에 BACPAC 파일을 로컬 저장소 또는 온-프레미스 위치의 Azure Blob 저장소에 저장할 수 있습니다. Azure SQL 데이터베이스 또는 SQL Server 설치 온-프레미스로 다시 가져올 수도 있습니다.

## <a name="considerations"></a>고려 사항

* 보관 파일이 트랜잭션 일치하도록 내보내기 중에나 Azure SQL 데이터베이스의 [트랜잭션 일치 복사본](sql-database-copy.md) 에서 내보내기 중에는 쓰기 활동이 발생하지 않도록 해야 합니다.
* Azure Blob 저장소에 보관된 BACPAC 파일의 최대 크기는 200GB입니다. 이보다 큰 BACPAC 파일을 로컬 저장소에 보관하려면 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령 프롬프트 유틸리티를 사용합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다. 최신 버전의 SQL Server Data Tools를 [다운로드](https://msdn.microsoft.com/library/mt204009.aspx) 하여 이 유틸리티를 가져올 수도 있습니다.
* BACPAC 파일을 사용하여 Azure 프리미엄 저장소에 보관하는 것은 지원되지 않습니다.
* 내보내기 작업이 20시간을 초과하면 취소될 수 있습니다. 내보내는 중에 성능을 향상시키기 위해 다음을 수행할 수 있습니다.
  * 서비스 수준을 일시적으로 높이기
  * 내보내기 중에 모든 읽기 및 쓰기 작업 중단
  * 모든 대형 테이블에 null이 아닌 값의 [클러스터형 인덱스](https://msdn.microsoft.com/library/ms190457.aspx) 를 사용합니다. 클러스터형 인덱스가 없는 경우 6~12시간 이상 소요되면 내보내기에 실패할 수 있습니다. 전체 테이블 내보내기를 시도하려면 내보내기 서비스에서 테이블 스캔을 완료해야 하기 때문입니다. 테이블이 내보내기에 최적화되었는지 확인하는 좋은 방법은 **DBCC SHOW_STATISTICS**를 실행하고 *RANGE_HI_KEY*가 null이 아닌지와 해당 값이 잘 배포되어 있는지 검토하는 것입니다. 자세한 내용은 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)를 참조하세요.

> [!NOTE]
> BACPAC는 백업에 사용되는 목적이 아니며 작업을 복원합니다. Azure SQL 데이터베이스에서는 모든 사용자 데이터베이스의 백업이 자동으로 생성됩니다. 자세한 내용은 [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)을 참조하세요.
> 
> 

이 문서를 완료하려면 다음이 필요합니다.

* Azure 구독.
* Azure SQL 데이터베이스입니다.
* 표준 저장소에 BACPAC를 저장할 Blob 컨테이너가 있는 [Azure 표준 저장소 계정](../storage/storage-create-storage-account.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>데이터베이스 내보내기
[New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx) cmdlet은 서비스에 내보내기 데이터베이스 요청을 제출합니다. 데이터베이스 크기에 따라 내보내기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

> [!IMPORTANT]
> BACPAC 파일의 트랜잭션 일관성을 위해 먼저 [데이터베이스의 사본을 만든 다음](sql-database-copy-powershell.md)데이터베이스 사본을 내보냅니다.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>내보내기 작업의 진행률 모니터링
[New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx)를 실행한 후 [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)를 실행하여 요청 상태를 확인할 수 있습니다. 이 요청 직후에 이 명령을 실행하면 **Status : InProgress**가 반환됩니다. **Status : Succeeded**가 표시되면 내보내기가 완료된 것입니다.

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

Azure SQL Database 자동화된 내보내기는 현재 미리 보기이며 및 2017년 3월 1일에 사용이 중지됩니다. 2016년 12월 1일을 시작으로 모든 SQL Database에서 자동화된 내보내기를 더는 구성할 수 없습니다. 기존의 모든 자동화된 내보내기 작업은 2017년 3월 1일까지 계속 수행됩니다. 2016년 12월 1일 후에는 [장기 백업 보존](sql-database-long-term-retention.md) 또는 [Azure Automation](../automation/automation-intro.md)을 사용하여 선택한 일정에 따라 주기적으로 PowerShell을 사용해 주기적으로 SQL Database를 보관할 수 있습니다. 샘플 스크립트의 경우, [Github에서 샘플 스크립트](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)를 다운로드할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
* Powershell을 사용하여 Azure SQL 데이터베이스를 가져오는 방법을 알아보려면 [PowerShell을 사용하여 BACPAC 가져오기](sql-database-import-powershell.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)




<!--HONumber=Dec16_HO3-->


