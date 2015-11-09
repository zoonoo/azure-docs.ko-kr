<properties 
    pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스의 BACPAC 만들기 및 내보내기" 
    description="PowerShell을 사용하여 Azure SQL 데이터베이스의 BACPAC 만들기 및 내보내기" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/23/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell을 사용하여 Azure SQL 데이터베이스의 BACPAC 만들기 및 내보내기

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


이 문서에서는 PowerShell을 사용하여 Azure SQL 데이터베이스의 BACPAC를 내보내는 방법에 대한 지침을 제공합니다.

[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)는 데이터베이스 스키마 및 데이터를 포함하는 .bacpac 파일입니다. BACPAC의 기본 사용 사례는 하나의 서버에서 다른 서버로 데이터베이스를 이동하고, [로컬 데이터베이스를 클라우드에 마이그레이션](sql-database-cloud-migrate.md)하고, 개방형 형식에서 기존 데이터베이스를 보관하는 것입니다.

> [AZURE.NOTE]BACPAC는 백업에 사용되는 목적이 아니며 작업을 복원합니다. Azure SQL 데이터베이스에서는 모든 사용자 데이터베이스의 백업이 자동으로 생성됩니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.


작업이 성공적으로 완료되면 다운로드할 수 있는 BACPAC이 Azure 저장소 Blob 컨테이너로 내보내집니다.


이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.
- BACPAC을 저장할 Blob 컨테이너가 있는 [Azure 저장소 계정](storage-create-storage-account.md). 현재 저장소 계정은 클래식 배포 모델을 사용해야 하므로 저장소 계정을 만들 때 **클래식**을 선택합니다.
- Azure PowerShell. [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행하여 Azure PowerShell 모듈을 다운로드하고 설치할 수 있습니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하세요.



## 자격 증명 구성 및 구독 선택

먼저 Azure 계정에 액세스 권한을 설정해야 하므로 PowerShell을 시작하고 다음 cmdlet을 실행합니다. 로그인 화면에서 Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 입력합니다.

	Add-AzureAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.


### Azure 구독 선택

구독을 선택하려면 구독 ID 또는 구독 이름(**-SubscriptionName**)이 필요합니다. 이전 단계에 표시된 정보에서 구독 ID를 복사하거나, 구독이 여러 개이고 세부 정보가 필요한 경우 **Get-AzureSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다. 구독을 설정한 후 다음 cmdlet을 실행합니다.

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

성공적으로 **Select-azuresubscription**을 실행한 후 PowerShell 프롬프트로 돌아갑니다. 둘 이상의 구독이 있는 경우 **Get-azuresubscription**을 실행하고 사용하려는 구독이 **IsCurrent: True**를 표시하는지 확인할 수 있습니다.


## 특정 환경에 맞게 변수 설정

예제 값을 데이터베이스 및 저장소 계정에 대한 특정 값으로 바꿔야 하는 몇 개의 변수가 있습니다.

서버 및 데이터베이스 이름을 현재 계정에 있는 서버 및 데이터베이스로 바꿉니다. Blob 이름에 생성되는 BACPAC 파일 이름을 입력합니다. 원하는 대로 BACPAC 파일의 이름을 입력할 수 있지만 .bacpac 확장명을 포함해야 합니다.

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetoexport"
    $BlobName = "filename.bacpac"

[Azure Preview 포털](https://portal.azure.com)에서 해당 저장소 계정을 찾아 이러한 값을 얻습니다. 저장소 계정 블레이드에서 **모든 설정**, **키**를 차례로 클릭하여 기본 선택키를 찾을 수 있습니다.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## 서버 및 저장소 계정에 대한 포인터 만들기

**Get-Credential** cmdlet을 실행하면 사용자 이름 및 암호를 묻는 창이 열립니다. SQL Server에 대한 관리자 로그인 및 암호를 입력합니다(Azure 계정의 사용자 이름 및 암호 아님).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## 데이터베이스 내보내기

이 명령은 데이터베이스 내보내기 요청을 서비스에 제출합니다. 데이터베이스 크기에 따라 내보내기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

> [AZURE.IMPORTANT]BACPAC 파일의 트랜잭션 일관성을 위해 먼저 [데이터베이스의 사본을 만든 다음](sql-database-copy-powershell.md) 데이터베이스 사본을 내보냅니다.


    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## 내보내기 작업의 진행률 모니터링

**Start-AzureSqlDatabaseExport**를 실행한 후 요청 상태를 확인할 수 있습니다. 요청 직후에 실행하면 일반적으로 **상태: 보류 중** 또는 **상태: 실행 중**이 반환되므로 **상태: 완료**가 출력에 표시될 때까지 여러 번 실행할 수 있습니다.

이 명령을 실행하면 암호를 묻는 메시지가 표시됩니다. SQL Server에 대한 관리자 암호를 입력합니다.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## SQL 데이터베이스 내보내기 PowerShell 스크립트


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "databasename"
    $BlobName = "bacpacfilename"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## 다음 단계

- [Azure SQL 데이터베이스 가져오기](sql-database-import-powershell.md)


## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Nov15_HO1-->