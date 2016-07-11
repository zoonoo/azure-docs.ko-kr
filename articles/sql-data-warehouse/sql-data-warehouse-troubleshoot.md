<properties
   pageTitle="Azure SQL 데이터 웨어하우스 문제 해결 | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스 문제 해결"
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
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL 데이터 웨어하우스 문제 해결
이 항목에서는 Azure SQL 데이터 웨어하우스를 사용하는 발생할 수 있는 일반적인 문제 중 일부를 설명합니다.


##연결 오류

연결 문제가 발생하는 경우 고객들이 일반적으로 보고하는 다음의 몇 가지 문제점을 참조할 수 있습니다.

### CTAIP 오류
이 오류는 로그인이 SQL 데이터 웨어하우스 데이터베이스가 아닌 SQL 서버 마스터 데이터베이스에서 만들어진 경우에 발생할 수 있습니다. 이 오류가 발생하는 경우 [보안 개요][] 문서를 살펴보세요. 이 문서에서는 마스터 데이터베이스에서 로그인을 만드는 방법과 SQL 데이터 웨어하우스 데이터베이스에서 사용자를 만드는 방법을 차례로 설명합니다.

### 방화벽 규칙
Azure SQL 데이터베이스가 알려진 IP 주소만 데이터베이스에 액세스할 수 있도록 서버 및 데이터베이스 수준 방화벽으로 보호됩니다. 방화벽은 기본적으로 안전하며 이는 연결하기 전에 IP 주소 또는 주소 범위를 명시적으로 설정해야 한다는 의미입니다. 액세스를 위해 방화벽을 구성하려면 [프로비전 지침][]의 [클라이언트 IP에 대한 서버 방화벽 액세스 구성][] 단계를 따르세요.

### 지원되지 않는 도구/프로토콜
SQL 데이터 웨어하우스에서는 [Visual Studio 2013 또는 2015][]를 사용하여 데이터를 쿼리할 것을 권장합니다. 클라이언트 연결의 경우 [SQL Server Native Client 10/11(ODBC)][]이 권장됩니다. SSMS(SQL Server Management Studio)는 아직 지원되지 않지만 부분적으로 작동하며, 개체 탐색기 트리는 SQL 데이터 웨어하우스에서 작동하지 않으며 쿼리는 일부 오류 메시지를 무시한 후 작동할 수 있습니다.


## 성능 문제

쿼리 성능을 향상시키는 방법은 [SQL 데이터 웨어하우스 모범 사례][] 문서에서 알아볼 수 있습니다. 특정 쿼리 문제를 해결하려는 경우 [쿼리 모니터링 방법 알아보기][] 문서가 유용합니다. 때때로 쿼리를 더 빠르게 실행하기 위한 솔루션은 [SQL 데이터 웨어하우스를 조정][]하여 단순히 더 많은 계산 능력을 쿼리에 추가하는 것입니다.

## 클러스터된 Columnstore 세그먼트 품질

클러스터된 Columnstore 세그먼트 품질은 클러스터된 Columnstore 테이블에서 최적의 쿼리 성능을 제공하는 데 중요합니다. 세그먼트 품질은 압축된 행 그룹에 있는 행의 수로 측정할 수 있습니다. 다음 쿼리는 Columnstore 인덱스 세그먼트 상태가 나쁜 테이블을 식별하고 이러한 테이블에서 Columnstore 인덱스를 다시 빌드하기 위한 T-SQL을 생성합니다. 이 쿼리 결과의 첫 번째 열에는 각 인덱스를 다시 빌드하기 위한 T-SQL이 표시됩니다. 두 번째 열에는 압축을 최적화하는 데 사용할 최소 리소스 클래스에 대한 권장 사항이 표시됩니다.
 
**1단계:** 각 SQL 데이터 웨어하우스 데이터베이스에서 이 쿼리를 실행하여 최적이 아닌 클러스터 columnstore 인덱스를 식별합니다. 행이 반환되지 않으면 추가 작업이 필요하지 않습니다.

```sql
SELECT 
     'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;' AS [T-SQL to Rebuild Index]
    ,CASE WHEN n.nbr_nodes < 3 THEN 'xlargerc' WHEN n.nbr_nodes BETWEEN 4 AND 6 THEN 'largerc' ELSE 'mediumrc' END AS [Resource Class Recommendation]
    ,s.name AS [Schema Name]
    ,t.name AS [Table Name]
    ,AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) AS [Ave Rows in Compressed Row Groups]
FROM 
    sys.pdw_nodes_column_store_row_groups rg
    JOIN sys.pdw_nodes_tables pt 
        ON rg.object_id = pt.object_id AND rg.pdw_node_id = pt.pdw_node_id AND pt.distribution_id = rg.distribution_id
    JOIN sys.pdw_table_mappings tm 
        ON pt.name = tm.physical_name
    INNER JOIN sys.tables t 
        ON tm.object_id = t.object_id
INNER JOIN sys.schemas s
    ON t.schema_id = s.schema_id
CROSS JOIN (SELECT COUNT(*) nbr_nodes  FROM sys.dm_pdw_nodes WHERE type = 'compute') n
GROUP BY 
    n.nbr_nodes, s.name, t.name
HAVING 
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000 OR
    AVG(CASE WHEN rg.State = 0 THEN rg.Total_rows ELSE NULL END) < 100000

ORDER BY 
    s.name, t.name
```
 
**2단계:** 이 테이블의 인덱스를 위 쿼리의 2번째 열의 권장 리소스 클래스에 다시 빌드할 수 있는 권한이 있는 사용자의 리소스 클래스를 증가합니다.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  위의 LoadUser는 ALTER INDEX 문을 실행할 수 있도록 유용한 사용자로 만들어야 합니다. db\_owner 사용자의 리소스 클래스는 변경할 수 없습니다. 리소스 클래스에 대한 자세한 내용과 새 사용자를 만드는 방법은 아래 링크를 참조하십시오.

 
**3단계:** 이제 더 높은 리소스 클래스를 사용 중인 2단계의 사용자(예: "LoadUser")로 로그온하고 1단계의 쿼리에서 생성한 ALTER INDEX 문을 실행합니다. 이 사용자가 1단계 쿼리에서 식별한 테이블에 대한 ALTER 권한이 있는지 확인합니다.
 
**4단계:** 1단계의 쿼리를 다시 실행합니다. 인덱스가 효율적으로 구축된 경우 이 쿼리에서 반환되는 행이 없어야 합니다. 반환되는 행이 없으면 완료된 것입니다. SQL DW 데이터베이스가 여러 개인 경우에는 각 데이터베이스에서 이 프로세스를 반복합니다. 반환된 행이 있을 경우 5단계에서 계속합니다.
 
**5단계:** 1단계의 쿼리를 다시 실행할 때 반환되는 행이 있을 경우 매우 넓은 행이 포함된 테이블이 있어 클러스터된 columnstore 인덱스를 최적으로 구축할 때 너무 많은 양의 메모리가 필요할 수 있습니다. 이 경우 xlargerc 클래스를 사용하여 해당 테이블에 대해 이 프로세스를 다시 시도합니다. 리소스 클래스를 변경할 경우 xlargerc를 사용하여 2단계를 반복합니다. 그런 다음 최적이 아닌 인덱스가 포함된 테이블에 대해 3단계를 반복합니다. DW100 - DW300을 사용하고 있고 이미 xlargerc를 사용한 경우에는 인덱스를 그대로 두거나 일시적으로 DWU를 높여 이 작업에 추가 메모리를 제공할 수 있습니다.
 
**마지막 단계:** 위에서 지정한 리소스 클래스는 가장 높은 품질의 columnstore 인덱스를 구축하는 데 권장되는 최소 리소스 클래스입니다. 데이터를 로드하는 사용자에 대해 이 설정을 유지하는 것이 좋습니다. 하지만 2단계의 변경 사항을 취소하려면 다음 명령을 실행하십시오.

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```

CCI 테이블에 대한 부하의 최소 리소스 클래스는 DW100-DW300의 경우 xlargerc, DW400-DW600의 경우 largerc, DW1000 이상에는 mediumrc를 사용하는 것이 좋습니다. 이 지침은 대부분의 워크로드에 적합합니다. 목표는 각 인덱스 빌드 작업에 400MB 이상의 메모리를 할당하는 것입니다. 하지만 모든 상황에 일괄적인 지침은 아닙니다. columnstore 인덱스를 최적화하는 데 필요한 메모리는 로드 중인 데이터에 따라 달라지며, 기본적으로 행 크기의 영향을 받습니다. 행 너비가 좁은 테이블은 더 적은 메모리가 필요하며 행 너비가 넓은 테이블은 더 많은 메모리가 필요합니다. 실험해 보고 싶은 경우 1단계의 쿼리를 사용하여 작은 메모리 할당 시 최적의 columnstore 인덱스를 얻을 수 있는지 확인해 보십시오. 적어도 행 그룹당 평균 10만 개 이상의 행이 적합합니다. 50만 개 이상이면 더 좋습니다. 결과적으로 행 그룹당 최대 1백만 개의 행이 포함됩니다. 리소스 클래스 및 동시성을 관리하는 방법은 아래 링크를 참조하십시오.


## 다음 단계

위 문제에 대한 해결 방법을 찾을 수 없으면 다음에 나오는 몇 가지 다른 리소스를 참조할 수 있습니다.

- [블로그]
- [기능 요청]
- [비디오]
- [CAT 팀 블로그]
- [지원 티켓 만들기]
- [MSDN 포럼]
- [Stack Overflow 포럼]
- [Twitter]

<!--Image references-->

<!--Article references-->
[보안 개요]: ./sql-data-warehouse-overview-manage-security.md
[지원 티켓 만들기]: ./sql-data-warehouse-get-started-create-support-ticket.md
[SQL 데이터 웨어하우스를 조정]: ./sql-data-warehouse-manage-compute-overview.md
[쿼리 모니터링 방법 알아보기]: ./sql-data-warehouse-manage-monitor.md
[프로비전 지침]: ./sql-data-warehouse-get-started-provision.md
[클라이언트 IP에 대한 서버 방화벽 액세스 구성]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 또는 2015]: ./sql-data-warehouse-get-started-connect.md
[SQL 데이터 웨어하우스 모범 사례]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[SQL Server Native Client 10/11(ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other Web references-->
[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 포럼]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow 포럼]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0629_2016-->