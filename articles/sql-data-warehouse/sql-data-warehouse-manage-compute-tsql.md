<properties
   pageTitle="Azure SQL 데이터 웨어하우스의 계산 능력 관리(REST) | Microsoft Azure"
   description="DWU를 조정하여 성능을 확장하는 Transact-SQL (T-SQL) 작업입니다. 사용량이 많지 않은 시간 동안 다시 조정하여 비용을 절감합니다."
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
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스의 계산 능력 관리(T-SQL)

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-manage-compute-overview.md)
- [포털](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


워크로드의 변화하는 요구를 충족시키도록 계산 리소스와 메모리를 확장하여 크기 조정을 실시합니다. 사용량이 많지 않은 시간 동안 리소스를 다시 조정하거나 계산 전체를 일시 중지하여 비용을 절감합니다.

이 작업 컬렉션은 T-SQL을 사용하여 다음을 수행합니다.

- 현재 DWU 설정 보기
- Dwu를 조정하여 계산 리소스 변경

데이터베이스를 일시 중지 또는 다시 시작하려면 이 문서의 맨 위에 있는 다른 플랫폼 옵션 중 하나를 선택합니다.

이에 대해 알아보려면 [계산 능력 관리 개요][]를 참조하세요.

<a name="current-dwu-bk"></a>

## 현재 DWU 설정 보기

데이터베이스의 현재 DWU 설정을 보려면

1. Visual Studio 2015에서 SQL Server 개체 탐색기를 엽니다.
2. 논리적 SQL 데이터베이스 서버와 연결된 마스터 데이터베이스에 연결합니다.
2. sys.database\_service\_objectives 동적 관리 뷰에서 선택합니다. 다음은 예제입니다.

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a> <a name="scale-compute-bk"></a>

## 계산 조정

[AZURE.INCLUDE [SQL 데이터 웨어하우스 크기 조정 DWU 설명](../../includes/sql-data-warehouse-scale-dwus-description.md)]

DWU를 변경하려면


1. 논리적 SQL 데이터베이스 서버와 연결된 마스터 데이터베이스에 연결합니다.
2. [ALTER DATABASE][] TSQL 문을 사용합니다. 다음 예제에서는 MySQLDW 데이터베이스에 대한 서비스 수준 목표를 DW1000으로 설정합니다.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## 다음 단계

다른 관리 작업은 [관리 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[관리 개요]: ./sql-data-warehouse-overview-manage.md
[계산 능력 관리 개요]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0810_2016-->