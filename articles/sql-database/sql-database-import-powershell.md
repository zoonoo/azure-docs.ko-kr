<properties 
    pageTitle="PowerShell을 사용하여 BACPAC 파일을 가져와 새 Azure SQL 데이터베이스 만들기 | Microsoft Azure" 
    description="PowerShell을 사용하여 BACPAC 파일을 가져와 새 Azure SQL 데이터베이스 만들기" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="01/20/2016"
    ms.author="sstein"/>

# PowerShell을 사용하여 BACPAC 파일을 가져와 새 Azure SQL 데이터베이스 만들기

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

이 문서에서는 PowerShell을 사용하여 BACPAC를 가져와 Azure SQL 데이터베이스를 만드는 방법에 대한 지침을 제공합니다.

BACPAC은 데이터베이스 스키마 및 데이터를 포함하는 .bacpac 파일입니다. 자세한 내용은 [데이터 계층 응용 프로그램](https://msdn.microsoft.com/library/ee210546.aspx)에서 백업 패키지(.bacpac)를 참조하세요.

데이터베이스는 Azure 저장소 Blob 컨테이너에서 가져온 BACPAC으로 만들어집니다. Azure 저장소에 .bacpac 파일이 없는 경우 [Azure SQL 데이터베이스의 BACPAC 만들기 및 내보내기](sql-database-export-powershell.md)의 단계에 따라 새로 만들 수 있습니다.

> [AZURE.NOTE]Azure SQL 데이터베이스는 모든 사용자 데이터베이스에 대해 복원할 수 있는 백업을 자동으로 만들고 유지 관리합니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.


SQL 데이터베이스를 가져오려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- 복원할 데이터베이스의 .bacpac 파일(BACPAC). BACPAC은 [Azure 저장소 계정(클래식)](storage-create-storage-account.md) Blob 컨테이너에 있어야 합니다.


> [AZURE.IMPORTANT]이 문서에서는 Azure PowerShell 버전 1.0 미만의 명령을 포함합니다. **Get-Module azure | format-table version** 명령으로 Azure PowerShell의 버전을 확인할 수 있습니다.



## 자격 증명 구성 및 구독 선택

먼저 Azure 계정에 액세스 권한을 설정해야 하므로 PowerShell을 시작하고 다음 cmdlet을 실행합니다. 로그인 화면에서 Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 입력합니다.

	Add-AzureAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.


### Azure 구독 선택

구독을 선택하려면 구독 ID가 필요합니다. 이전 단계에 표시된 정보에서 구독 ID를 복사하거나, 구독이 여러 개이고 세부 정보가 필요한 경우 **Get-AzureSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다. 구독 ID를 얻은 후 다음 cmdlet을 실행합니다.

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

성공적으로 **Select-azuresubscription**을 실행한 후 PowerShell 프롬프트로 돌아갑니다. 둘 이상의 구독이 있는 경우 **Get-azuresubscription**을 실행하고 선택한 구독이 **IsCurrent: True**를 표시하는지 확인합니다.


## 사용자 환경에 맞게 변수 설정

예제 값을 데이터베이스 및 저장소 계정에 대한 특정 값으로 바꿔야 하는 몇 개의 변수가 있습니다.

서버 이름은 이전 단계에서 선택한 구독에 현재 존재하는 서버여야 하며, 데이터베이스를 만들려는 서버입니다.

데이터베이스 이름은 새 데이터베이스에 사용할 이름입니다.

    $ServerName = "servername"
    $DatabaseName = "databasename"


다음 변수는 BACPAC이 있는 저장소 계정에서 제공됩니다. [Azure 포털](https://portal.azure.com)에서 해당 저장소 계정을 찾아 이러한 값을 얻습니다. 저장소 계정 블레이드에서 **모든 설정**, **키**를 차례로 클릭하여 기본 액세스 키를 찾을 수 있습니다.

Blob 이름은 데이터베이스를 만들려는 기존 .bacpac 파일의 이름입니다. .bacpac 확장명을 포함해야 합니다.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## 서버 및 저장소 계정에 대한 포인터 만들기

**Get-Credential** cmdlet을 실행하면 사용자 이름 및 암호를 묻는 창이 열립니다. 데이터베이스를 만들려는 SQL Server(위의 $ServerName)에 대한 관리자 로그인 및 암호를 입력합니다(Azure 계정의 사용자 이름 및 암호 아님).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## 데이터베이스 가져오기

이 명령은 데이터베이스 가져오기 요청을 서비스에 제출합니다. 데이터베이스 크기에 따라 가져오기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## 작업의 진행률 모니터링

**Start-AzureSqlDatabaseImport**를 실행한 후 요청 상태를 확인할 수 있습니다.

요청 직후에 상태를 확인하면 일반적으로 **보류 중** 또는 **실행 중** 상태가 반환되고 현재 완료율이 제공되므로 **상태: 완료**가 출력에 표시될 때까지 여러 번 실행할 수 있습니다.

이 명령을 실행하면 암호를 묻는 메시지가 표시됩니다. SQL Server에 대한 관리자 로그인 및 암호를 입력합니다.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## SQL 데이터베이스 PowerShell 복원 스크립트


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## 다음 단계

- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)




## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0121_2016-->