<properties
   pageTitle="Azure SQL 데이터 웨어하우스용 PowerShell cmdlet"
   description="데이터베이스를 일시 중지하고 다시 시작하는 방법을 포함하여 Azure SQL 데이터 웨어하우스용 PowerShell cmdlet을 확인합니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/02/2016"
   ms.author="sonyama;barbkess;mausher"/>

# SQL 데이터 웨어하우스용 PowerShell cmdlet 및 REST API

SQL 데이터 웨어하우스는 Azure PowerShell cmdlet 또는 REST API를 사용하여 관리할 수 있습니다.

**Azure SQL 데이터베이스**에 대해 정의된 대부분의 명령을 **SQL 데이터 웨어하우스**에도 사용할 수 있습니다. 현재 목록은 [Azure SQL Cmdlet](https://msdn.microsoft.com/library/mt574084.aspx)을 참조하세요. cmdlet [Suspend-AzureRmSqlDatabase][] 및 [Resume-AzureRmSqlDatabase][]는 SQL 데이터 웨어하우스에 대해 설계된 추가 항목입니다.

마찬가지로, **SQL Azure 데이터베이스**에 대한 REST Api를 **SQL 데이터 웨어하우스** 인스턴스에도 사용할 수 있습니다. 현재 목록의 경우에는 [Azure SQL 데이터베이스용 작업](https://msdn.microsoft.com/library/azure/dn505719.aspx)을 참조하세요.

## Azure PowerShell Cmdlet 시작

1. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://aka.ms/webpi-azps)를 실행합니다. 이 설치 관리자에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법][]을 참조하세요.
2. PowerShell을 시작하려면 **시작**을 클릭하고 **Windows PowerShell**을 입력합니다.
3. PowerShell 프롬프트에서 다음 명령을 실행하여 Azure Resource Manager에 로그인하고 구독을 선택합니다.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```


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


## SQL 데이터 웨어하우스 예제

다음은 SQL 데이터 웨어하우스에만 적용되는 기능에 대한 예제입니다.

### [Suspend-AzureRmSqlDatabase][]

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

### [Resume-AzureRmSqlDatabase][]

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


## 다음 단계
자세한 참조 정보는 [SQL 데이터 웨어하우스 참조 개요][]를 참조하세요. 더 많은 PowerShell 예제는 다음을 참조하세요.
- [Powershell을 사용하여 SQL 데이터 웨어하우스 만들기](sql-data-warehouse-get-started-provision-powershell.md)
- [스냅숏에서 복원](sql-data-warehouse-backup-and-restore-from-snapshot.md)
- [스냅숏에서 지역 복원](sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md)

<!--Image references-->

<!--Article references-->
[SQL 데이터 웨어하우스 참조 개요]: sql-data-warehouse-overview-reference.md
[Azure PowerShell을 설치 및 구성하는 방법]: ../articles/powershell-install-configure.md

<!--MSDN references-->
[Get AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureRmSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619337.aspx



<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->