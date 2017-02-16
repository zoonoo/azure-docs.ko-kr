---
title: "Azure SQL Data Warehouse의 계산 능력 관리(PowerShell) | Microsoft Docs"
description: "계산 능력을 관리하는 PowerShell 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5749b37408be8eeab6a3fba40d6768ca1d035096


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Azure SQL 데이터 웨어하우스의 계산 능력 관리(PowerShell)
> [!div class="op_single_selector"]
> * [개요](sql-data-warehouse-manage-compute-overview.md)
> * [포털](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST (영문)](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

워크로드의 변화하는 요구를 충족시키도록 계산 리소스와 메모리를 확장하여 크기 조정을 실시합니다. 사용량이 많지 않은 시간 동안 리소스를 다시 조정하거나 계산 전체를 일시 중지하여 비용을 절감합니다.

이 작업 컬렉션은 Azure 포털을 사용하여 다음을 수행합니다.

* 계산 조정
* 계산 일시 중지
* 계산 다시 시작

이에 대해 알아보려면 [계산 관리 개요][Manage compute overview]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
### <a name="install-the-latest-version-of-azure-powershell"></a>Azure PowerShell 최신 버전 설치
> [!NOTE]
> SQL 데이터 웨어하우스에서 Azure PowerShell을 사용하려면 Azure PowerShell 버전 1.0.3 이상을 설치해야 합니다.  현재 버전을 확인하려면 **Get-Module -ListAvailable -Name Azure**명령을 실행합니다. [Microsoft 웹 플랫폼 설치 관리자][Microsoft Web Platform Installer]를 통해 최신 버전을 설치할 수 있습니다.  자세한 내용은 [Azure PowerShell 설치 및 구성 방법][How to install and configure Azure PowerShell]을 참조하세요.
> 
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell Cmdlet 시작
시작하기:

1. Azure PowerShell을 엽니다.
2. PowerShell 프롬프트에서 다음 명령을 실행하여 Azure Resource Manager에 로그인하고 구독을 선택합니다.
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>계산 능력 크기 조정
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU를 변경하려면 [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase] PowerShell cmdlet을 사용합니다. 다음 예제에서는 MyServer에서 호스팅되는 MySQLDW 데이터베이스에 대한 서비스 수준 목표를 DW1000으로 설정합니다.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>계산 일시 중지
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

데이터베이스를 일시 중지하려면 [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase] cmdlet을 사용합니다. 다음 예에서는 Server01 서버에서 호스트하는 이름이 Database02인 데이터베이스를 일시 중지합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

> [!NOTE]
> 서버가 foo.database.windows.net인 경우 PowerShell cmdlet의 ServerName으로 "foo"를 사용합니다.
> 
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
다음 예에서는 이를 변형하여 데이터베이스를 $database 개체로 검색합니다. 그런 다음 개체를 [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]에 파이프합니다. 결과는 resultDatabase 개체에 저장됩니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>계산 다시 시작
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

데이터베이스를 시작하려면 [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase] cmdlet을 사용합니다. 다음 예에서는 Server01 서버에서 호스팅되는 이름이 Database02인 데이터베이스를 시작합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

다음 예에서는 이를 변형하여 데이터베이스를 $database 개체로 검색합니다. 그런 다음 개체를 [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]에 파이프하고 결과를 $resultDatabase에 저장합니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>다음 단계
다른 관리 작업은 [관리 개요][Management overview]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/



<!--HONumber=Dec16_HO2-->


