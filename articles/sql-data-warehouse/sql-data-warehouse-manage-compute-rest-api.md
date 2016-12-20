---
title: "Azure SQL Data Warehouse의 계산 능력 관리(REST) | Microsoft Docs"
description: "계산 능력을 관리하는 PowerShell 작업 DWU를 조정하여 계산 리소스 크기를 조정합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: barbkess
editor: 
ms.assetid: 21de7337-9356-49bb-a6eb-06c1beeba2c4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dcf7fa98ce35e330e88d9263cc71ce85e592be3a


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Azure SQL 데이터 웨어하우스의 계산 능력 관리(REST)
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

이에 대해 알아보려면 [계산 관리 개요][계산 관리 개요]를 참조하세요.

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>계산 능력 크기 조정
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU를 변경하려면 [데이터베이스 생성 또는 업데이트][데이터베이스 생성 또는 업데이트] REST API를 사용합니다. 다음 예제에서는 MyServer에서 호스팅되는 MySQLDW 데이터베이스에 대한 서비스 수준 목표를 DW1000으로 설정합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>계산 일시 중지
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

데이터베이스를 일시 중지하려면 [데이터베이스 일시 중지][데이터베이스 일시 중지] REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스트하는 이름이 Database02인 데이터베이스를 일시 중지합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>계산 다시 시작
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

데이터베이스를 시작하려면 [데이터베이스 다시 시작][데이터베이스 다시 시작] REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스팅되는 이름이 Database02인 데이터베이스를 시작합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다. 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>다음 단계
다른 관리 작업은 [관리 개요][관리 개요]를 참조하세요.

<!--Image references-->

<!--Article references-->
[관리 개요]: ./sql-data-warehouse-overview-manage.md
[계산 관리 개요]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[데이터베이스 일시 중지]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[데이터베이스 다시 시작]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[데이터베이스 생성 또는 업데이트]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure Portal]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


