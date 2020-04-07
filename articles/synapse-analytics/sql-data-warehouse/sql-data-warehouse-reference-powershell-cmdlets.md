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
ms.openlocfilehash: f3d6d0c1f71e2262e943998cdc08717291903365
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743828"
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

변형, 이 예제는 검색된 개체를 [일시 중단-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)에 파이프합니다.  그 결과로 데이터베이스가 일시 중지됩니다. 마지막 명령은 결과를 보여 줍니다.

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

변형인 이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스를 검색합니다. 검색된 개체를 [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)에 파이프합니다.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> 서버가 foo.database.windows.net인 경우 PowerShell cmdlet의 ServerName으로 "foo"를 사용합니다.

## <a name="other-supported-powershell-cmdlets"></a>지원되는 기타 PowerShell cmdlet

이러한 PowerShell cmdlet은 Azure 시냅스 분석 데이터 웨어하우스에서 지원됩니다.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [겟-아즈Sql데이터베이스복원점](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [뉴 아즈Sql데이터베이스](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [복원-AzSqlDatabase]/파워쉘/모듈/az.sql/restore-azsqldatabase?toc=/azure/시냅스 분석/sql-데이터 웨어하우스/toc.json&bc=/azure/시냅스 분석/sql 데이터 웨어하우스/이동 경로/toc.json)
* [이력서-아즈Sql데이터베이스](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [일시 중단-아즈Sql데이터베이스](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>다음 단계

더 많은 PowerShell 예제는 다음을 참조하세요.

* [PowerShell을 사용하여 데이터 웨어하우스 만들기](create-data-warehouse-powershell.md)
* [데이터베이스 복원](sql-data-warehouse-restore-points.md)

PowerShell으로 자동화할 수 있는 다른 작업에 대 한는 [Azure SQL Database cmdlet]/powershell/모듈/az.sql?toc=/azure/시냅스 분석/sql-데이터 웨어하우스/toc.json&bc=/azure/시냅스 분석/sql 데이터 웨어하우스/이동 경로/toc.json)을 참조하십시오. Azure 시냅스 분석 데이터 웨어하우스에 대해 모든 Azure SQL Database cmdlet이 지원되는 것은 아닙니다. REST로 자동화할 수 있는 작업 목록은 [Azure SQL Database에 대한 작업을](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)참조하십시오.
