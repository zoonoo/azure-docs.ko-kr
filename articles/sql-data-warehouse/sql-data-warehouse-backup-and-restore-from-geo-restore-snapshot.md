<properties
   pageTitle="SQL 데이터 웨어하우스의 가동 중단에서 데이터베이스 복구 | Microsoft Azure"
   description="SQL 데이터 웨어하우스의 가동 중단에서 데이터베이스를 복구하기 위한 단계"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="sahajs;barbkess"/>

# SQL 데이터 웨어하우스의 가동 중단에서 데이터베이스 복구

지역 복원을 통해 지역 중복 백업에서 데이터베이스를 복원하여 새 데이터베이스를 만들 수 있습니다. Azure 지역의 모든 서버에서 데이터베이스를 만들 수 있습니다. 지역 중복 백업을 해당 원본으로 사용하므로 가동 중단으로 인해 데이터베이스에 액세스할 수 없는 경우에도 데이터베이스를 복구하는 데 사용할 수 있습니다. 가동 중단에서 복구하는 것 외에도 다른 서버나 지역에 데이터베이스 복사 또는 마이그레이션과 같은 다른 시나리오에 대해서도 지역 복원을 사용할 수 있습니다.


## 복구를 시작해야 하는 시기
복구 작업을 수행하려면 복구 시 SQL 연결 문자열을 변경해야 하며 변경 시 데이터가 영구적으로 손상될 수도 있습니다. 따라서 중단이 응용 프로그램의 RTO보다 오래 지속될 가능성이 있을 때만 복구를 수행해야 합니다. 다음 데이터 요소를 사용하여 복구가 보장되는지 확인합니다.

- 데이터베이스에 대한 영구 연결 실패
- Azure 포털에 광범위한 영향이 있는 지역 내 인시던트에 대한 경고가 표시됩니다.


## 지역 복원을 사용한 복구
데이터베이스 복구는 최신 지역 중복 백업에서 새 데이터베이스를 만듭니다. 복구하는 서버에 새 데이터베이스를 위한 충분한 DTU 용량이 있는지 확인하는 것이 중요합니다. [지원 센터에 연락][]하여 이 할당량을 늘리도록 요청할 수 있습니다.


### Azure 포털
1. [Azure 포털][]에 로그인합니다.
2. 화면 왼쪽에서 **새로 만들기**를 선택한 다음 **데이터 및 저장소**를 선택하고 **SQL 데이터 웨어하우스**를 선택합니다.
3. 원본으로 **백업**을 선택한 다음 복구에 사용할 지역 중복 백업을 선택합니다.
4. 나머지 데이터베이스 속성을 지정하고 **만들기**를 클릭합니다.
5. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.


### PowerShell
프로그래밍 방식으로 데이터베이스 복구를 수행하려면 Azure PowerShell을 사용합니다. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다. Get-Module -ListAvailable -Name Azure를 실행하여 버전을 확인할 수 있습니다. 이 문서는 Microsoft Azure PowerShell 버전 1.0.4를 기반으로 합니다.

데이터베이스를 복구하려면 [Start-AzureSqlDatabaseRecovery][] cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터베이스가 포함된 구독을 선택합니다.
4. 복구하려는 데이터베이스를 가져옵니다.
5. 데이터베이스 복구 요청을 만듭니다.
6. 복구 진행률을 모니터링합니다.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$Database = Get-AzureRmSqlRecoverableDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Recover database
$RecoveryRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourSourceServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -TargetServerName "<YourTargetServerName>"

# Monitor progress of recovery operation
Get-AzureSqlDatabaseOperation -ServerName "<YourTargetServerName>" –OperationGuid $RecoveryRequest.RequestID

```

서버가 foo.database.windows.net인 경우 위의 PowerShell cmdlet에서 -ServerName으로 "foo"를 사용합니다.

### REST API
프로그래밍 방식으로 데이터베이스 복구를 수행하려면 REST를 사용합니다.

1. [복구 가능한 데이터베이스 나열][] 작업을 통해 복구 가능한 데이터베이스 목록을 가져옵니다.
2. [복구 가능한 데이터베이스 가져오기][] 작업을 통해 복구할 데이터베이스를 가져옵니다.
3. [데이터베이스 복구 요청 만들기][] 작업을 통해 복구 요청을 만듭니다.
4. [데이터베이스 작업 상태][] 작업을 통해 복구 상태를 추적합니다.



## 복구 후 데이터베이스 구성
복구된 데이터베이스 프로덕션을 준비하는 데 유용한 검사 목록입니다.

1. **연결 문자열 업데이트**: 클라이언트 도구의 연결 문자열이 새로 복구된 데이터베이스를 가리키는지 확인합니다.
2. **방화벽 규칙 수정**: 대상 서버에서 방화벽 규칙을 확인하고 서버 및 새로 복구된 데이터베이스와 클라이언트 컴퓨터 또는 Azure의 연결이 설정되어 있는지 확인합니다.
3. **서버 로그인 및 데이터베이스 사용자 확인**: 응용 프로그램에서 사용하는 모든 로그인이 복구된 데이터베이스를 호스팅하는 서버에 존재하는지 확인합니다. 누락된 로그인을 다시 만들고 복구된 데이터베이스에 대한 적절한 사용 권한을 부여합니다. 
4. **감사 사용**: 데이터베이스에 액세스하기 위해 감사가 필요한 경우 데이터베이스 복구 후에 감사 사용을 설정해야 합니다.

원본 데이터베이스가 TDE를 사용할 수 있는 경우 복구된 데이터베이스도 TDE를 사용할 수 있습니다.


## 다음 단계
다른 Azure SQL 데이터베이스 버전의 비즈니스 연속성 기능에 대해 알아보려면 [Azure SQL 데이터베이스 비즈니스 연속성 개요][]를 읽으세요.


<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 비즈니스 연속성 개요]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Start-AzureSqlDatabaseRecovery]: https://msdn.microsoft.com/library/azure/dn720224.aspx
[복구 가능한 데이터베이스 나열]: http://msdn.microsoft.com/library/azure/dn800984.aspx
[복구 가능한 데이터베이스 가져오기]: http://msdn.microsoft.com/library/azure/dn800985.aspx
[데이터베이스 복구 요청 만들기]: http://msdn.microsoft.com/library/azure/dn800986.aspx
[데이터베이스 작업 상태]: http://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Other Web references-->
[Azure 포털]: https://portal.azure.com/
[지원 센터에 연락]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0330_2016-->