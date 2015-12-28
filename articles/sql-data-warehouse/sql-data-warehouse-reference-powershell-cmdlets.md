<properties
   pageTitle="SQL 데이터 웨어하우스에서 cmdlet 시작"
   description="PowerShell cmdlet을 사용하여 SQL 데이터 웨어하우스 일시 중단 후 다시 시작"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sidneyh"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/15/2015"
   ms.author="twounder;sidneyh;barbkess"/>

# Azure 데이터 웨어하우스 cmdlet 및 REST API 시작

SQL 데이터 웨어하우스는 Azure PowerShell cmdlet 또는 REST API를 사용하여 관리할 수 있습니다.

**Azure SQL 데이터베이스**에 대해 정의된 명령을 **SQL 데이터 웨어하우스**에도 사용할 수 있습니다. 현재 목록은 [Azure SQL Cmdlet](https://msdn.microsoft.com/library/mt574084.aspx)을 참조하세요. cmdlet **Suspend-AzureSqlDatabase** 및 **Resume-AzureSqlDatabase**(아래)는 SQL 데이터 웨어하우스에 대해 설계된 추가 항목입니다.

마찬가지로, **SQL Azure 데이터베이스**에 대한 REST Api를 **SQL 데이터 웨어하우스** 인스턴스에도 사용할 수 있습니다. 현재 목록의 경우에는 [Azure SQL 데이터베이스용 작업](https://msdn.microsoft.com/library/azure/dn505719.aspx)을 참조하세요.

## Azure PowerShell cmdlet을 다운로드하여 실행합니다.

1. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다. 
2. 모듈을 실행하려면 시작 창에서 **Microsoft Azure PowerShell**을 입력합니다.
3. 아직 컴퓨터에 계정을 추가하지 않았다면 다음 cmdlet을 실행합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법]()을 참조하세요.

		Add-AzureAccount
3. 다음 cmdlet 사용하여 모드를 전환합니다.

		Switch-AzureMode AzureResourceManager

## Suspend-AzureSqlDatabase
### 예 1: 서버에서 이름으로 데이터베이스 일시 중지

이 예제에서는 "Server01."라는 서버에서 호스트하는 "Database02"라는 데이터베이스를 일시 중지합니다. 서버는 "ResourceGroup1."이라는 Azure 리소스 그룹 내에 있습니다.

    Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
    "Server01" –DatabaseName "Database02"

### 예 2: 데이터베이스 개체 일시 중지

이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스를 검색 합니다. 검색된 개체를 **Suspend AzureSqlDatabase**에 파이프합니다. 그 결과로 데이터베이스가 일시 중지됩니다.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Suspend-AzureSqlDatabase

## Resume-AzureSqlDatabase

### 예 1: 서버에서 이름으로 데이터베이스 다시 시작

이 예제에서는 "Server01."이라는 서버에서 호스트하는 "Database02"라는 데이터베이스의 작동을 다시 시작합니다. 서버는 "ResourceGroup1."이라는 리소스 그룹 내에 있습니다.

	Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"

### 예 2: 데이터베이스 개체 다시 시작

이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스를 검색합니다. 개체가 **Resume-AzureSqlDatabase**에 파이프됩니다.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Resume-AzureSqlDatabase

## Get-AzureSqlDatabaseRestorePoints

이 cmdlet은 Azure SQL 데이터베이스에 대한 백업 복원 지점을 나열합니다. 복원 지점은 데이터베이스를 복원하는 데 사용됩니다. 반환된 개체에 대한 속성은 다음과 같습니다.

속성|설명
---|---
RestorePointType|DISCRETE / CONTINUOUS. 불연속 복원 지점은 Azure SQL 데이터베이스를 복원할 수 있는 가능한 지정 시간을 설명합니다. 연속 복원 지점은 Azure SQL 데이터베이스를 복원할 수 있는 가장 빠른 지정 시간을 설명합니다. 데이터베이스를 가장 빠른 지점 후 아무 지정 시간으로나 복원할 수 있습니다.
EarliestRestoreDate|가장 빠른 복원 시간(restorePointType = CONTINUOUS인 경우 채워짐)
RestorePointCreationDate |백업 스냅숏 시간(restorePointType = DISCRETE인 경우 채워짐)

### 예 1: 서버에서 이름으로 데이터베이스의 복원 지점 검색
이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스에 대한 복원 지점을 검색합니다.

	$restorePoints = Get-AzureSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"



### 예 2: 데이터베이스 개체 다시 시작

이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스를 검색합니다. 데이터베이스 개체는 **Get-AzureSqlDatabase**로 파이프되며 결과가 데이터베이스의 복원 지점입니다.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$restorePoints = $database | Get-AzureSqlDatabaseRestorePoints



> [AZURE.NOTE]서버가 foo.database.windows.net인 경우 Powershell cmdlet에서 -ServerName로 "foo"를 사용합니다.


## 다음 단계
자세한 참조 정보는 [SQL 데이터 웨어하우스 참조 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[SQL 데이터 웨어하우스 참조 개요]: sql-data-warehouse-overview-reference.md
[How to install and configure Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_1217_2015-->