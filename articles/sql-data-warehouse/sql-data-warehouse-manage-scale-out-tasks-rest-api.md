<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 확장성 작업 관리(REST) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스 성능을 확장하기 위한 REST API 작업. DWU를 조정하여 계산 리소스를 변경합니다. 또는 계산 리소스를 일지 중지한 다음 다시 시작하여 비용을 절감합니다."
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

# Azure SQL 데이터 웨어하우스의 확장성 작업 관리(REST)

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-overview-scalability.md)
- [포털](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

변화하는 워크로드 수요에 맞게 계산 리소스와 메모리를 탄력적으로 확장하고 사용량이 많지 않을 때는 다시 리소스를 축소하여 비용을 절감합니다.

이 작업 컬렉션은 REST API를 사용하여 다음을 수행합니다.

- DWU를 조정하여 성능 조정
- 계산 리소스 일시 중지
- 계산 리소스 다시 시작

자세한 내용은 [성능 확장성 개요][]를 참조하세요.

<a name="scale-performance-bk"></a>

## 성능 조정

[AZURE.INCLUDE [SQL 데이터 웨어하우스 크기 조정 DWU 설명](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU를 변경하려면 [데이터베이스 생성 또는 업데이트][] REST API를 사용합니다. 다음 예제에서는 MyServer에서 호스팅되는 MySQLDW 데이버에스에 대한 서비스 수준 목표를 DW1000으로 설정합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

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

## 계산 일시 중지

[AZURE.INCLUDE [SQL 데이터 웨어하우스 일시 중지 설명](../../includes/sql-data-warehouse-pause-description.md)]

데이터베이스를 일시 중지하려면 [데이터베이스 일시 중지][] REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스트하는 이름이 Database02인 데이터베이스를 일시 중지합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## 계산 다시 시작

[AZURE.INCLUDE [SQL 데이터 웨어하우스 다시 시작 설명](../../includes/sql-data-warehouse-resume-description.md)]

데이터베이스를 시작하려면 [데이터베이스 다시 시작][] REST API를 사용합니다. 다음 예에서는 Server01 서버에서 호스팅되는 이름이 Database02인 데이터베이스를 시작합니다. 서버는 이름이 ResourceGroup1인 Azure 리소스 그룹 내에 있습니다.

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## 다음 단계

다른 관리 작업은 [관리 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[관리 개요]: ./sql-data-warehouse-overview-manage.md
[성능 확장성 개요]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[데이터베이스 일시 중지]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[데이터베이스 다시 시작]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[데이터베이스 생성 또는 업데이트]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->