---
title: "PowerShell을 사용하여 BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 8d78da13-43fe-4447-92e0-0a41d0321fd4
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/31/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 211f416d05b0ca998cd71a78d091b8efa39f6a7b


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>PowerShell을 사용하여 BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

이 문서에서는 PowerShell을 통해 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일을 가져와 Azure SQL 데이터베이스를 만드는 방법에 대한 지침을 제공합니다.

데이터베이스는 Azure Storage Blob 컨테이너에서 가져온 BACPAC 파일(.bacpac)로 만들어집니다. Azure Storage에 BACPAC 파일이 없는 경우 [PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관](sql-database-export-powershell.md)을 참조하세요. Azure Storage에 없는 BACPAC 파일이 이미 있는 경우 [AzCopy를 사용하여 Azure Storage 계정에 쉽게 업로드합니다](../storage/storage-use-azcopy.md#blob-upload).

> [!NOTE]
> Azure SQL 데이터베이스는 모든 사용자 데이터베이스에 대해 복원할 수 있는 백업을 자동으로 만들고 유지 관리합니다. 자세한 내용은 [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)을 참조하세요.
> 
> 

SQL 데이터베이스를 가져오려면 다음이 필요합니다.

* Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판** 을 클릭하고 되돌아와 이 문서를 완료합니다.
* 가져올 데이터베이스의 BACPAC 파일. BACPAC은 [Azure Storage 계정](../storage/storage-create-storage-account.md) Blob 컨테이너에 있어야 합니다.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="set-up-the-variables-for-your-environment"></a>사용자 환경에 맞게 변수 설정
예제 값을 데이터베이스 및 저장소 계정에 대한 특정 값으로 바꿔야 하는 몇 개의 변수가 있습니다.

서버 이름은 이전 단계에서 선택한 구독에 현재 존재하는 서버여야 합니다. 데이터베이스를 만들 서버여야 합니다. 탄력적 풀로 데이터베이스를 직접 가져오는 것은 지원되지 않습니다. 그렇지만 먼저 단일 데이터베이스로서 가져온 다음 해당 데이터베이스를 풀로 이동할 수 있습니다.

데이터베이스 이름은 새 데이터베이스에 사용할 이름입니다.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


다음 변수는 BACPAC이 있는 저장소 계정에서 제공됩니다. [Azure 포털](https://portal.azure.com)에서 해당 저장소 계정을 찾아 이러한 값을 얻습니다. 저장소 계정 블레이드에서 **모든 설정**, **키**를 차례로 클릭하여 기본 선택키를 찾을 수 있습니다.

Blob 이름은 데이터베이스를 만들려는 기존 BACPAC 파일의 이름입니다. .bacpac 확장명을 포함해야 합니다.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


[Get-Credential](https://msdn.microsoft.com/library/azure/hh849815\(v=azure.300\).aspx) cmdlet을 실행하면 사용자 이름 및 암호를 묻는 창이 열립니다. SQL 데이터베이스 서버(위의 $ServerName)의 관리자 로그인 및 암호를 입력합니다(Azure 계정의 사용자 이름 및 암호 아님).

    $credential = Get-Credential


## <a name="import-the-database"></a>데이터베이스 가져오기
이 명령은 데이터베이스 가져오기 요청을 서비스에 제출합니다. 데이터베이스 크기에 따라 가져오기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>작업의 진행률 모니터링
[New-AzureRmSqlDatabaseImport](https://msdn.microsoft.com/library/azure/mt707793\(v=azure.300\).aspx)를 실행한 후 [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)를 실행하여 요청 상태를 확인할 수 있습니다.

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>SQL 데이터베이스 PowerShell 내보내기 스크립트
    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -StorageKeytype $StorageKeyType -StorageKey $StorageKey -StorageUri $StorageUri -AdministratorLogin $credential.UserName -AdministratorLoginPassword $credential.Password -Edition Standard -ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>다음 단계
* 가져온 SQL 데이터베이스에 연결하고 쿼리하는 방법을 알아보려면 [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리 수행](sql-database-connect-query-ssms.md)




<!--HONumber=Dec16_HO3-->


