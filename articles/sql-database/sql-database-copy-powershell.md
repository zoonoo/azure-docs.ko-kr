<properties 
    pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스 복사 | Microsoft Azure" 
    description="PowerShell을 사용하여 Azure SQL 데이터베이스 사본 만들기" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/21/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell을 사용하여 Azure SQL 데이터베이스 복사

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)



다음 단계는 PowerShell에서 SQL데이터베이스 사본을 만드는 방법을 보여 줍니다. 데이터베이스 복사 작업은 [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx) cmdlet을 사용하여 SQL 데이터베이스를 새 데이터베이스에 복사합니다. 사본은 동일한 서버 또는 다른 서버에서 만든 데이터베이스의 스냅샷 백업입니다.

> [AZURE.NOTE] Azure SQL 데이터베이스는 모든 사용자 데이터베이스에 대해 복원할 수 있는 백업을 자동으로 만들고 유지 관리합니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

복사 프로세스가 완료되면 새 데이터베이스가 원본 데이터베이스와는 독립적으로 완벽히 작동하는 데이터베이스가 됩니다. 새 데이터베이스는 복사가 완료된 시스템의 원본 데이터베이스와 트랜잭션이 일치합니다. 데이터베이스 사본의 서버 계층과 성능 수준(가격 책정 계층)은 원본 데이터베이스와 동일합니다. 복사 완료 후 사본은 완전히 작동하는 독립 데이터베이스입니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다.


데이터베이스를 동일한 논리적 서버에 복사할 때 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하느 보안 주체는 새 데이터베이스의 데이터베이스 소유자(DBO)가 됩니다. 모든 데이터베이스 사용자, 권한 및 보안 식별자(SID)가 데이터베이스 사본에 복사됩니다.


이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.
- Azure PowerShell. [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행하여 Azure PowerShell 모듈을 다운로드하고 설치할 수 있습니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.



## 자격 증명 구성 및 구독 선택

먼저 Azure 계정에 액세스 권한을 설정해야 하므로 PowerShell을 시작하고 다음 cmdlet을 실행합니다. 로그인 화면에서 Azure 클래식 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 입력합니다.

	Add-AzureAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.


### Azure 구독 선택

구독을 선택하려면 구독 ID 또는 구독 이름(**-SubscriptionName**)이 필요합니다. 이전 단계에 표시된 정보에서 구독 ID를 복사하거나, 구독이 여러 개이고 세부 정보가 필요한 경우 **Get-AzureSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다. 구독을 설정한 후 다음 cmdlet을 실행합니다.

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

성공적으로 **Select-azuresubscription**을 실행한 후 PowerShell 프롬프트로 돌아갑니다. 둘 이상의 구독이 있는 경우 **Get-azuresubscription**을 실행하고 사용하려는 구독이 **IsCurrent: True**를 표시하는지 확인할 수 있습니다.


## 특정 환경에 맞게 변수 설정

예제 값을 데이터베이스 및 서버에 대한 특정 값으로 바꿔야 하는 몇 개의 변수가 있습니다.

자리 표시자 값을 사용자 환경에 대 한 값으로 바꿉니다.

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





## 동일한 서버에 SQL 데이터베이스 복사

이 명령은 데이터베이스 복사 요청을 서비스에 제출합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

## SQL 데이터베이스를 다른 서버에 복사

이 명령은 데이터베이스 복사 요청을 서비스에 제출합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## 복사 작업 진행률 모니터링

**Start-AzureSqlDatabaseCopy**를 실행한 후 요청 상태를 확인할 수 있습니다. 요청 직후에 실행하면 일반적으로 **상태: 보류 중** 또는 **상태: 실행 중**이 반환되므로 **상태: 완료**가 출력에 표시될 때까지 여러 번 실행할 수 있습니다.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## SQL 데이터베이스 복사 PowerShell 스크립트

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## 다음 단계

- [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 수행합니다.](sql-database-connect-query-ssms.md)
- [데이터베이스를 BACPAC로 내보내기](sql-database-export-powershell.md)


## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0323_2016-->