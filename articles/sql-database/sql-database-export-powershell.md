<properties 
    pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관" 
    description="PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


이 문서에서는 PowerShell을 사용하여 Azure Blob 저장소에 저장된 BACPAC 파일로 Azure SQL 데이터베이스를 보관하기 위한 지침을 제공합니다.

Azure SQL 데이터베이스의 보관 파일을 만들어야 하는 경우 데이터베이스 스키마 및 데이터를 BACPAC 파일로 내보낼 수 있습니다. BACPAC 파일은 BACPAC의 확장명을 가진 단순한 ZIP 파일입니다. BACPAC 파일은 나중에 Azure Blob 저장소 또는 온-프레미스 저장소의 로컬 저장소에 저장할 수 있으며 나중에 Azure SQL 데이터베이스 또는 SQL Server 온-프레미스 설치로 다시 가져올 수 있습니다.

***고려 사항***

- 보관 파일이 트랜잭션 측면에서 일치하도록 내보내기 중에나 Azure SQL 데이터베이스의 [트랜잭션 일치 복사본](sql-database-copy.md)에서 내보내기 중에는 쓰기 활동이 발생하지 않도록 해야 합니다.
- Azure Blob 저장소에 보관된 BACPAC 파일의 최대 크기는 200GB입니다. 이보다 큰 BACPAC 파일을 로컬 저장소에 보관하려면 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령 프롬프트 유틸리티를 사용합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다. 최신 버전의 SQL Server Data Tools를 [다운로드](https://msdn.microsoft.com/library/mt204009.aspx)하여 이 유틸리티를 가져올 수도 있습니다.
- BACPAC 파일을 사용하여 Azure 프리미엄 저장소에 보관하는 것은 지원되지 않습니다.
- 내보내기 작업 20시간 초과되면 취소될 수 있습니다. 내보내는 중에 성능을 향상시키기 위해 다음을 수행할 수 있습니다.
 - 서비스 수준 임시로 향상
 - 내보내기 중에 모든 읽기 및 쓰기 작업 중단
 - 모든 대형 테이블에 클러스터형 인덱스 사용 클러스터형 인덱스가 없는 경우 6~12시간 이상 소요되면 내보내기에 실패할 수 있습니다. 전체 테이블 내보내기를 시도하려면 내보내기 서비스에서 테이블 스캔을 완료해야 하기 때문입니다.
 
> [AZURE.NOTE] BACPAC는 백업에 사용되는 목적이 아니며 작업을 복원합니다. Azure SQL 데이터베이스에서는 모든 사용자 데이터베이스의 백업이 자동으로 생성됩니다. 자세한 내용은 [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)을 참조하세요.

이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독.
- Azure SQL 데이터베이스입니다.
- 표준 저장소에 BACPAC를 저장할 Blob 컨테이너가 있는 [Azure 표준 저장소 계정](../storage/storage-create-storage-account.md).


[AZURE.INCLUDE [PowerShell 세션 시작](../../includes/sql-database-powershell.md)]


## 특정 환경에 맞게 변수 설정

예제 값을 데이터베이스 및 저장소 계정에 대한 특정 값으로 바꿔야 하는 몇 개의 변수가 있습니다.

특정 값으로 바꿉니다.

    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"


[Azure 포털](https://portal.azure.com)에서 해당 저장소 계정을 찾아 이러한 값을 얻습니다. 저장소 계정 블레이드에서 **모든 설정**, **키**를 차례로 클릭하여 기본 액세스 키를 찾을 수 있습니다.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


**Get-Credential** cmdlet을 실행하면 사용자 이름 및 암호를 묻는 창이 열립니다. SQL Server에 대한 관리자 로그인 및 암호를 입력합니다(Azure 계정의 사용자 이름 및 암호 아님).

    $credential = Get-Credential

## 데이터베이스 내보내기

이 명령은 데이터베이스 내보내기 요청을 서비스에 제출합니다. 데이터베이스 크기에 따라 내보내기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

> [AZURE.IMPORTANT] BACPAC 파일의 트랜잭션 일관성을 위해 먼저 [데이터베이스의 사본을 만든 다음](sql-database-copy-powershell.md) 데이터베이스 사본을 내보냅니다.


    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password
    

## 내보내기 작업의 진행률 모니터링

**New-AzureRmSqlDatabaseExport**를 실행한 후 요청 상태를 확인할 수 있습니다. 요청 직후에 실행하면 일반적으로 **상태: 보류 중** 또는 **상태: 실행 중**이 반환되므로 **상태: 완료**가 출력에 표시될 때까지 여러 번 실행할 수 있습니다.

이 명령을 실행하면 암호를 묻는 메시지가 표시됩니다. SQL Server에 대한 관리자 암호를 입력합니다.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink
    


## SQL 데이터베이스 내보내기 PowerShell 스크립트


    $ServerName = "servername"
    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName  –AdministratorPassword $credential.Password

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink


## 다음 단계

- Powershell을 사용하여 Azure SQL 데이터베이스를 가져오는 방법을 알아보려면 [PowerShell을 사용하여 BACPAC 가져오기](sql-database-import-powershell.md)를 참조하세요.

<!---HONumber=AcomDC_0727_2016-->