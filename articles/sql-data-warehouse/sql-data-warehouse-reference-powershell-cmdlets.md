<properties
   pageTitle="Azure SQL 데이터 웨어하우스용 PowerShell cmdlet"
   description="데이터베이스를 일시 중지하고 다시 시작하는 방법을 포함하여 Azure SQL 데이터 웨어하우스용 PowerShell cmdlet을 확인합니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/18/2016"
   ms.author="sonyama;barbkess;mausher"/>

# SQL 데이터 웨어하우스용 PowerShell cmdlet 및 REST API

많은 SQL 데이터 웨어하우스 관리 작업을 Azure PowerShell cmdlet 또는 REST API를 사용하여 관리할 수 있습니다. 다음은 PowerShell 명령을 사용하여 SQL 데이터 웨어하우스의 일반적인 작업을 자동화하는 방법에 대한 몇 가지 예제입니다. 유용한 REST 예제는 [Manage scalability with REST][]\(REST를 사용하여 확장성 관리) 문서를 참조하세요.

> [AZURE.NOTE]  SQL 데이터 웨어하우스에서 Azure PowerShell을 사용하려면 Azure PowerShell 버전 1.0.3 이상을 설치해야 합니다. **Get-Module -ListAvailable -Name Azure**를 실행하여 버전을 확인할 수 있습니다. 최신 버전은 [Microsoft 웹 플랫폼 설치 관리자][]를 통해 설치할 수 있습니다. 최신 버전 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법][]을 참조하세요.

## Azure PowerShell Cmdlet 시작

1. Windows PowerShell을 엽니다. 
2. PowerShell 프롬프트에서 다음 명령을 실행하여 Azure Resource Manager에 로그인하고 구독을 선택합니다.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## SQL 데이터 웨어하우스 일시 중지 예제

"Server01."라는 서버에서 호스트하는 "Database02"라는 데이터베이스를 일시 중지합니다. 서버는 "ResourceGroup1."이라는 Azure 리소스 그룹 내에 있습니다.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
변형인 이 예제에서는 검색된 개체를 [Suspend-AzureRmSqlDatabase][]에 파이프합니다. 그 결과로 데이터베이스가 일시 중지됩니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## SQL 데이터 웨어하우스 시작 예제

"Server01."이라는 서버에서 호스트하는 "Database02"라는 데이터베이스의 작동을 다시 시작합니다. 서버는 "ResourceGroup1."이라는 리소스 그룹 내에 있습니다.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

변형인 이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스를 검색합니다. 검색된 개체를 [Resume-AzureRmSqlDatabase][]에 파이프합니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] 서버가 foo.database.windows.net인 경우 PowerShell cmdlet의 ServerName으로 "foo"를 사용합니다.

## 자주 사용되는 PowerShell cmdlet

다음 PowerShell cmdlet은 Azure SQL 데이터 웨어하우스에서 자주 사용됩니다.

- [Get AzureRmSqlDatabase][]
- [Get AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [New-AzureRmSqlDatabase][]
- [Remove-AzureRmSqlDatabase][]
- [Restore-AzureRmSqlDatabase][] 
- [Resume-AzureRmSqlDatabase][]
- [Select-AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspend-AzureRmSqlDatabase][]

## 다음 단계
더 많은 PowerShell 예제는 다음을 참조하세요.

- [Powershell을 사용하여 SQL 데이터 웨어하우스 만들기][]
- [스냅숏에서 복원][]
- [스냅숏에서 지역 복원][]

PowerShell로 자동화할 수 있는 모든 작업 목록은 [Azure SQL 데이터베이스 Cmdlet][]을 참조하세요. REST로 자동화할 수 있는 작업 목록은 [Azure SQL 데이터베이스에 대한 작업][]을 참조하세요.

<!--Image references-->

<!--Article references-->
[Azure PowerShell 설치 및 구성 방법]: ./powershell-install-configure.md
[Powershell을 사용하여 SQL 데이터 웨어하우스 만들기]: ./sql-data-warehouse-get-started-provision-powershell.md
[스냅숏에서 복원]: ./sql-data-warehouse-backup-and-restore-from-snapshot.md
[스냅숏에서 지역 복원]: ./sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md

<!--MSDN references-->
[Azure SQL 데이터베이스 Cmdlet]: https://msdn.microsoft.com/library/mt574084.aspx
[Azure SQL 데이터베이스에 대한 작업]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft 웹 플랫폼 설치 관리자]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0525_2016-->