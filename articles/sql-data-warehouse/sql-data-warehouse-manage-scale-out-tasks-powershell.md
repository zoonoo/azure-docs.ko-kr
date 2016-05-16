<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 확장성 작업 관리(PowerShell) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스 성능을 확장하기 위한 Powershell 작업. DWU를 조정하여 계산 리소스를 변경합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스의 확장성 작업 관리(PowerShell)

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-overview-scalability.md)
- [포털](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

변화하는 워크로드 수요에 맞게 계산 리소스와 메모리를 탄력적으로 확장하고 사용량이 많지 않을 때는 다시 리소스를 축소하여 비용을 절감합니다.

이 작업 컬렉션에서는 PowerShell cmdlet을 사용하여 다음을 수행합니다.

- DWU를 조정하여 성능 조정
- 계산 리소스 일시 중지
- 계산 리소스 다시 시작

자세한 내용은 [성능 확장성 개요][]를 참조하세요.

<a name="before-you-begin-bk"></a>

## 시작하기 전에

### Azure PowerShell 최신 버전 설치

> [AZURE.NOTE]  SQL 데이터 웨어하우스에서 Azure PowerShell을 사용하려면 Azure PowerShell 버전 1.0.3 이상을 설치해야 합니다. 현재 버전을 확인하려면 **Get-Module -ListAvailable -Name Azure** 명령을 실행합니다. [Microsoft 웹 플랫폼 설치 관리자][]를 통해 최신 버전을 설치할 수 있습니다. 자세한 내용은 [Azure PowerShell 설치 및 구성하는 방법]을 참조하세요.

### Azure PowerShell Cmdlet 시작

시작하기:

1. Azure PowerShell을 엽니다. 
2. PowerShell 프롬프트에서 다음 명령을 실행하여 Azure Resource Manager에 로그인하고 구독을 선택합니다.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>

## 성능 조정

[AZURE.INCLUDE [SQL 데이터 웨어하우스 크기 조정 DWU 설명](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU를 변경하려면 [Set-AzureRmSqlDatabase][] PowerShell cmdlet을 사용합니다. 다음 예제에서는 MyServer에서 호스팅되는 MySQLDW 데이버에스에 대한 서비스 수준 목표를 DW1000으로 설정합니다.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## 계산 일시 중지

[AZURE.INCLUDE [SQL 데이터 웨어하우스 일시 중지 설명](../../includes/sql-data-warehouse-pause-description.md)]

데이터베이스를 일시 중지하려면 [Suspend-AzureRmSqlDatabase][] cmdlet을 사용합니다. 다음 예에서는 Server01 서버에서 호스트하는 이름이 Database02인 데이터베이스를 일시 중지합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

> [AZURE.NOTE] 서버가 foo.database.windows.net인 경우 PowerShell cmdlet의 ServerName으로 "foo"를 사용합니다.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
다음 예에서는 이를 변형하여 데이터베이스를 $database 개체로 검색합니다. 그런 다음 개체를 [Suspend-AzureRmSqlDatabase][]에 파이프합니다. 결과는 resultDatabase 개체에 저장됩니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## 계산 다시 시작

[AZURE.INCLUDE [SQL 데이터 웨어하우스 다시 시작 설명](../../includes/sql-data-warehouse-resume-description.md)]

데이터베이스를 시작하려면 [Resume-AzureRmSqlDatabase][] cmdlet을 사용합니다. 다음 예에서는 Server01 서버에서 호스팅되는 이름이 Database02인 데이터베이스를 시작합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

다음 예에서는 이를 변형하여 데이터베이스를 $database 개체로 검색합니다. 그런 다음 개체를 [Resume-AzureRmSqlDatabase][]에 파이프하고 결과를 $resultDatabase에 저장합니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## 다음 단계

다른 관리 작업은 [관리 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[관리 개요]: ./sql-data-warehouse-overview-manage.md
[성능 확장성 개요]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->