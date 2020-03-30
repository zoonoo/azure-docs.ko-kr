---
title: 파워쉘 & 레스트 API
description: 데이터베이스를 일시 중지하고 다시 시작하는 방법을 포함하여 Azure Synapse Analytics SQL 풀에 대한 상위 PowerShell cmdlet을 찾습니다.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351382"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>Azure 시냅스 분석 SQL 풀에 대한 PowerShell & REST API

많은 Azure 시냅스 분석 SQL 풀 관리 작업은 Azure PowerShell cmdlet 또는 REST API를 사용하여 관리할 수 있습니다.  다음은 PowerShell 명령을 사용하여 SQL 풀에서 일반적인 작업을 자동화하는 방법의 몇 가지 예입니다.  유용한 REST 예제는 [REST를 사용하여 확장성 관리](sql-data-warehouse-manage-compute-rest-api.md) 문서를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell Cmdlet 시작

1. Windows PowerShell을 엽니다.
2. PowerShell 프롬프트에서 다음 명령을 실행하여 Azure Resource Manager에 로그인하고 구독을 선택합니다.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>데이터 웨어하우스 일시 중지 예제
"Server01."라는 서버에서 호스트하는 "Database02"라는 데이터베이스를 일시 중지합니다.  서버는 "ResourceGroup1."이라는 Azure 리소스 그룹 내에 있습니다.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

변형, 이 예제는 검색된 개체를 [일시 중단-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)에 파이프합니다.  그 결과로 데이터베이스가 일시 중지됩니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>데이터 웨어하우스 시작 예제

"Server01."이라는 서버에서 호스트하는 "Database02"라는 데이터베이스의 작동을 다시 시작합니다. 서버는 "ResourceGroup1."이라는 리소스 그룹 내에 있습니다.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

변형인 이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스를 검색합니다. 검색된 개체를 [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)에 파이프합니다.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> 서버가 foo.database.windows.net인 경우 PowerShell cmdlet의 ServerName으로 "foo"를 사용합니다.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>지원되는 기타 PowerShell cmdlet
이러한 PowerShell cmdlet은 Azure 시냅스 분석 데이터 웨어하우스에서 지원됩니다.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [겟-아즈Sql데이터베이스복원점](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [뉴 아즈Sql데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [이력서-아즈Sql데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [선택-아즈서브셀백서스](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [일시 중단-아즈Sql데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>다음 단계
더 많은 PowerShell 예제는 다음을 참조하세요.

* [PowerShell을 사용하여 데이터 웨어하우스 만들기](create-data-warehouse-powershell.md)
* [데이터베이스 복원](sql-data-warehouse-restore-points.md)

PowerShell을 사용하여 자동화할 수 있는 다른 작업에 대한 경우 [Azure SQL Database cmdlet](https://docs.microsoft.com/powershell/module/az.sql)을 참조하십시오. Azure 시냅스 분석 데이터 웨어하우스에 대해 모든 Azure SQL Database cmdlet이 지원되는 것은 아닙니다.  REST로 자동화할 수 있는 작업 목록은 [Azure SQL Database에 대한 작업을](/rest/api/sql/)참조하십시오.
