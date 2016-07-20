<properties 
    pageTitle="PowerShell을 사용하여 BACPAC 파일을 가져와 새 Azure SQL 데이터베이스 만들기 | Microsoft Azure" 
    description="PowerShell을 사용하여 BACPAC 파일을 가져와 새 Azure SQL 데이터베이스 만들기" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="07/06/2016"
    ms.author="sstein"/>

# PowerShell을 사용하여 BACPAC 파일을 가져와 새 Azure SQL 데이터베이스 만들기

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

이 문서에서는 PowerShell을 사용하여 BACPAC를 가져와 Azure SQL 데이터베이스를 만드는 방법에 대한 지침을 제공합니다.

BACPAC은 데이터베이스 스키마 및 데이터를 포함하는 .bacpac 파일입니다. 자세한 내용은 [데이터 계층 응용 프로그램](https://msdn.microsoft.com/library/ee210546.aspx)에서 백업 패키지(.bacpac)를 참조하세요.

데이터베이스는 Azure 저장소 Blob 컨테이너에서 가져온 BACPAC으로 만들어집니다. Azure 저장소에 .bacpac 파일이 없는 경우 [PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관](sql-database-export-powershell.md)의 단계에 따라 새로 만들 수 있습니다.

> [AZURE.NOTE] Azure SQL 데이터베이스는 모든 사용자 데이터베이스에 대해 복원할 수 있는 백업을 자동으로 만들고 유지 관리합니다. 자세한 내용은 [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)을 참조하세요.


SQL 데이터베이스를 가져오려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- 가져올 데이터베이스의 .bacpac 파일(BACPAC). BACPAC은 [Azure 저장소 계정(클래식)](../storage/storage-create-storage-account.md) Blob 컨테이너에 있어야 합니다.



[AZURE.INCLUDE [PowerShell 세션 시작](../../includes/sql-database-powershell.md)]



## 사용자 환경에 맞게 변수 설정

예제 값을 데이터베이스 및 저장소 계정에 대한 특정 값으로 바꿔야 하는 몇 개의 변수가 있습니다.

서버 이름은 이전 단계에서 선택한 구독에 현재 존재하는 서버여야 하며, 데이터베이스를 만들려는 서버입니다. 탄력적 풀에 직접 데이터베이스 가져오기는 지원되지 않지만 먼저 단일 데이터베이스로 가져온 다음 데이터베이스를 풀로 이동할 수 있습니다.

데이터베이스 이름은 새 데이터베이스에 사용할 이름입니다.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


다음 변수는 BACPAC이 있는 저장소 계정에서 제공됩니다. [Azure 포털](https://portal.azure.com)에서 해당 저장소 계정을 찾아 이러한 값을 얻습니다. 저장소 계정 블레이드에서 **모든 설정**, **키**를 차례로 클릭하여 기본 액세스 키를 찾을 수 있습니다.

Blob 이름은 데이터베이스를 만들려는 기존 .bacpac 파일의 이름입니다. .bacpac 확장명을 포함해야 합니다.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


**Get-Credential** cmdlet을 실행하면 사용자 이름 및 암호를 묻는 창이 열립니다. SQL 데이터베이스 서버(위의 $ServerName)의 관리자 로그인 및 암호를 입력합니다(Azure 계정의 사용자 이름 및 암호 아님).

    $credential = Get-Credential


## 데이터베이스 가져오기

이 명령은 데이터베이스 가져오기 요청을 서비스에 제출합니다. 데이터베이스 크기에 따라 가져오기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
    

## 작업의 진행률 모니터링

**New-AzureRmSqlDatabaseImport**를 실행한 후 요청 상태를 확인할 수 있습니다.

요청 직후에 상태를 확인하면 일반적으로 **보류 중** 또는 **실행 중** 상태가 반환되고 현재 완료율이 제공되므로 **상태: 완료**가 출력에 표시될 때까지 여러 번 실행할 수 있습니다.

이 명령을 실행하면 암호를 묻는 메시지가 표시됩니다. SQL Server에 대한 관리자 로그인 및 암호를 입력합니다.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink
 


## SQL 데이터베이스 PowerShell 내보내기 스크립트


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMxSize 50000
 
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink

    

## 다음 단계

- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)




## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0706_2016-->