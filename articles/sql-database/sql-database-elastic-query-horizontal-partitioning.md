<properties
    pageTitle="분할(행 분할)을 위한 탄력적 데이터베이스 쿼리 | Microsoft Azure"
    description="행 분할에서 탄력적 쿼리를 설정하는 방법"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/28/2016"
    ms.author="torsteng;sidneyh" />

# 분할(행 분할)을 위한 탄력적 데이터베이스 쿼리

이 문서에서는 행 분할 시나리오에서의 탄력적 데이터베이스 설정 방법과 쿼리 수행 방법을 설명합니다. 행 분할 시나리오의 정의는 [탄력적 데이터베이스 쿼리 개요(미리 보기)](sql-database-elastic-query-overview.md)를 참조하세요.

![분할된 데이터베이스에 대한 쿼리][1]

이 기능은 Azure SQL [데이터베이스 탄력적 데이터베이스 기능 집합](sql-database-elastic-scale-introduction.md)의 일부입니다.
 
## 데이터베이스 개체 만들기

탄력적 데이터베이스 쿼리는 분할(또는 행 분할)을 사용하여 여러 데이터베이스 간에 데이터를 배포하는 데이터 계층을 참조하도록 T-SQL 구문을 확장합니다. 이 섹션에서는 분할된 테이블에서 탄력적 쿼리와 연결된 DDL 문에 대한 개요를 제공합니다. 이 문은 탄력적 쿼리 데이터베이스에서 분할된 데이터 계층의 메타데이터 표현을 만듭니다. 이 문을 실행하기 위한 필수 구성 요소는 탄력적 데이터베이스 클라이언트 라이브러리를 사용하는 분할 맵을 만드는 것입니다. [분할 맵 관리](sql-database-elastic-scale-shard-map-management.md)를 참조하여 자세한 내용을 확인하거나, [탄력적 데이터베이스 도구 시작](sql-database-elastic-scale-get-started.md) 항목의 샘플을 사용하여 하나 만들어 봅니다.

탄력적 데이터베이스 쿼리에 대한 데이터베이스 개체 정의는 아래의 행 분할 시나리오를 상세히 설명하는 다음 T-SQL 문에 따릅니다.

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [데이터베이스 범위 자격 증명 만들기](https://msdn.microsoft.com/library/mt270260.aspx)

* [외부 데이터 원본 만들기/삭제](https://msdn.microsoft.com/library/dn935022.aspx)

* [외부 테이블 만들기/삭제](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 데이터베이스 범위 마스터 키 및 자격 증명 

자격 증명은 탄력적 쿼리가 Azure SQL DB의 원격 데이터베이스에 연결할 때 사용하는 사용자 ID와 암호를 나타냅니다. 필요한 마스터키 및 자격 증명을 만들려면 다음 구문을 사용합니다.

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]

또는 자격 증명과 키를 삭제하려면

    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
**참고** *< username>*에 *“@servername”* 접미사가 포함되지 않아야 합니다.

### 1\.2 외부 데이터 원본

외부 데이터 원본을 정의하여 분할 맵 및 데이터 계층에 대한 정보를 제공합니다. 외부 데이터 원본에는 분할 맵을 참조합니다. 그러면 탄력적 쿼리는 외부 데이터 원본과 기본 분할 맵을 사용하여 데이터 계층에 참여하는 데이터베이스를 열거합니다. 외부 데이터 원본을 만드는 구문은 다음과 같이 정의됩니다.

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 
 
또는 외부 데이터 원본을 삭제하려면:

	DROP EXTERNAL DATA SOURCE <data_source_name>[;] 

#### CREATE/DROP EXTERNAL DATA SOURCE 권한 

사용자는 모든 외부 데이터 원본 ALTER 권한이 있어야 합니다. 이 사용 권한은 ALTER DATABASE 권한에 포함 됩니다.

**예제**

다음 예제에서는 외부 데이터 원본에 대한 CREATE 문 사용을 보여줍니다.

	CREATE EXTERNAL DATA SOURCE MyExtSrc 
	WITH 
	( 
		TYPE=SHARD_MAP_MANAGER,
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ShardMapDatabase', 
		CREDENTIAL= SMMUser, 
		SHARD_MAP_NAME='ShardMap' 
	);
 
다음 카탈로그 뷰에서 현재 외부 데이터 원본의 목록을 검색할 수 있습니다.

	select * from sys.external_data_sources; 

탄력적 쿼리를 처리하는 동안 분할 데이터베이스 맵을 읽고 분할 데이터베이스의 데이터에 액세스할 때는 동일한 자격 증명을 사용합니다.

### 1\.3 외부 테이블 
 
탄력적 쿼리는 여러 데이터베이스에 걸쳐 행 분할되는 외부 테이블을 참조하도록 외부 테이블 DDL을 확장합니다. 외부 테이블 정의에서는 다음과 같은 측면을 다룹니다.

* **스키마**: 외부 테이블 DDL은 쿼리가 사용할 수 있는 스키마를 정의합니다. 외부 테이블 정의에서 제공한 스키마는 실제 데이터가 저장되는 분할 데이터베이스의 테이블 스키마와 일치해야 합니다. 

* **데이터 배포**: 외부 테이블 DDL은 데이터 계층에 걸쳐 데이터를 배포하는 데 사용하는 데이터 분산을 정의합니다. Azure SQL 데이터베이스는 분할된 데이터베이스에서의 실제 배포에 대해 외부 테이블에서 정의한 배포의 유효성을 확인하지 않습니다. 사용자가 직접 분할된 데이터베이스에서의 실제 데이터 배포가 외부 테이블 정의와 일치하는지 확인해야 합니다.

* **데이터 계층 참조**: 외부 테이블 DDL은 외부 데이터 원본을 가리킵니다. 외부 데이터 원본은 외부 테이블에 데이터 계층의 모든 데이터베이스를 찾는 데 필요한 정보를 제공하는 분할 맵을 지정합니다.

이전 섹션에서 설명한 대로 외부 데이터 소스를 사용하여 외부 테이블을 만들고 삭제하는 구문은 다음과 같습니다.

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

DATA\_SOURCE 절은 외부 테이블에 사용하는 외부 데이터 원본(행 분할의 경우 분할 맵)을 정의합니다.

SCHEMA\_NAME 및 OBJECT\_NAME 절은 각각 외부 테이블 정의를 분할에서 다른 스키마에 있는 테이블 또는 다른 이름의 테이블에 매핑하는 기능을 제공합니다. 생략한 경우 원격 개체의 스키마를 “dbo”라 가정하고 그 이름이 정의하는 외부 테이블 이름과 동일하다고 간주합니다.

SCHEMA\_NAME 및 OBJECT\_NAME 절은 특히 외부 테이블을 만들려는 데이터베이스에서 이미 원격 테이블의 이름을 가져온 경우에 유용합니다. 수평 확장된 데이터 계층에서 DMV의 카탈로그 뷰 집계를 가져오기 위해 외부 테이블을 정의하는 경우를 이 문제의 예로 들 수 있습니다. 카탈로그 뷰와 DMV는 이미 로컬로 존재하므로 외부 테이블 정의에 그 이름을 사용할 수 없습니다. 대신 다른 이름을 사용하고 SCHEMA\_NAME 및/또는 OBJECT\_NAME 절에서 카탈로그 뷰나 DMV의 이름을 사용합니다. 아래 예제를 참조하세요.

DISTRIBUTION 절은 이 테이블의 데이터 배포를 지정합니다.

* SHARDED는 이 테이블의 데이터가 분할 맵의 데이터베이스 전체에서 행 분할됨을 뜻합니다. 데이터 분포의 분할 키는 <sharding_column_name> 매개 변수에 있습니다.  

* REPLICATED는 테이블의 동일한 사본이 분할 맵의 각 데이터베이스에 있음을 의미합니다. Azure SQL DB는 테이블의 사본을 유지하지 않습니다. 사용자가 데이터베이스 전체에서 복제본이 동일한지 확인해야 합니다.

* ROUND\_ROBIN은 행 분할을 사용하여 테이블이 배포됨을 의미합니다. 그러나 응용 프로그램 종속 배포가 사용되었습니다.

쿼리 프로세서는 가장 효율적인 쿼리 계획을 구성하기 위해 DISTRIBUTION에서 제공하는 정보를 활용합니다.

외부 테이블을 삭제하려면 다음 문을 사용합니다.

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**CREATE/DROP EXTERNAL TABLE 권한**: ALTER ANY EXTERNAL DATA SOURCE 권한은 참조가 필요한 기본 데이터 원본이 필요합니다.

**보안 고려 사항:** 외부 테이블에 대한 액세스 권한이 있는 사용자는 외부 데이터 원본 정의에서 제공한 자격 증명에 따라 자동으로 기본 원격 테이블에 액세스할 수 있습니다. 외부 데이터 원본의 자격 증명을 통해 원치 않는 권한 상승을 방지하려면 외부 테이블에 대한 액세스 관리에 주의가 필요합니다. 일반 SQL 권한을 사용하여 일반 테이블에서처럼 외부 테이블에 대한 액세스를 부여하거나 취소할 수 있습니다.

**예제**: 다음 예제에서는 외부 테이블을 만드는 방법을 보여 줍니다.

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

다음 예제에서는 현재 데이터베이스에서 외부 테이블 목록을 검색 하는 방법을 보여줍니다.

	select * from sys.external_tables; 

## 쿼리 

### 2\.1 최고 충실도 T-SQL 쿼리 

외부 데이터 원본 및 외부 테이블을 정의한 후 외부 테이블을 통해 전체 T-SQL을 사용할 수 있습니다.

**행 분할 예제**: 다음 쿼리는 웨어하우스, 주문 및 주문 라인 간의 3방향 조인을 수행하고 여러 집계 및 선택적인 필터를 사용합니다. (1) 행 분할(분할)이며 (2) 웨어하우스, 주문 및 주문 라인이 웨어하우스 ID 열로 분할되었으며, 탄력적 쿼리가 분할된 데이터베이스에서 조인을 배치할 수 있고, 분할된 데이터베이스에서 비용이 드는 쿼리 부분을 처리할 수 있다고 가정합니다.

	select  
		 w_id as warehouse,
		 o_c_id as customer,
		 count(*) as cnt_orderline,
		 max(ol_quantity) as max_quantity,
		 avg(ol_amount) as avg_amount, 
		 min(ol_delivery_d) as min_deliv_date
	from warehouse 
	join orders 
	on w_id = o_w_id
	join order_line 
	on o_id = ol_o_id and o_w_id = ol_w_id 
	where w_id > 100 and w_id < 200 
	group by w_id, o_c_id 
 
### 2\.2 원격 T-SQL 실행을 위한 저장 프로시저

또한 탄력적 쿼리는 분할된 데이터베이스에 대한 직접 액세스를 제공하기 위해 저장 프로시저를 사용합니다. 저장 프로시저는 sp\_execute\_remote라고 하며, 원격 데이터베이스에서 원격 저장 프로시저 또는 T-SQL 코드를 실행하는 데 사용될 수 있습니다. 사용되는 매개 변수는 다음과 같습니다.
* 데이터 원본 이름(nvarchar): RDBMS 형식의 외부 데이터 원본 이름입니다. 
* 쿼리(nvarchar): T-SQL 쿼리를 각 분할된 데이터베이스에서 실행할 수 있습니다. 
* 매개 변수 선언(nvarchar), 선택 사항: 쿼리 매개 변수(예: sp\_executesql)에 사용된 매개 변수에 대한 데이터 형식 정의가 있는 문자열입니다. 
* 매개 변수 값 목록, 선택 사항: 쉼표로 구분한 매개 변수 값(예: sp\_executesql) 목록.

sp\_execute\_remote는 호출 매개 변수에 제공된 외부 데이터 원본을 사용하여 원격 데이터베이스에서 지정된 T-SQL 문을 실행합니다. 또한 외부 데이터 원본의 자격 증명을 사용하여 분할 맵 관리자 데이터베이스 및 원격 데이터베이스에 연결합니다.

예제:

	EXEC sp_execute_remote
		N'MyExtSrc',
		N'select count(w_id) as foo from warehouse' 

## 도구에 대한 연결  

일반 SQL Server 연결 문자열을 사용 하여 응용 프로그램, BI, 데이터 통합 도구를 외부 테이블 정의가 있는 데이터베이스에 연결합니다. SQL Server 도구에 대한 데이터 소스로 지원 되는지 확인 합니다. 그런 다음 도구에 연결된 타 SQL Server 데이터베이스 등, 탄력적 쿼리 데이터베이스를 참조하고 도구의나 응용 프로그램의 외부 테이블을 로컬 테이블처럼 사용합니다.

## 모범 사례 

* 탄력적 쿼리 끝점 데이터베이스에 SQL DB 방화벽을 통한 모든 분할된 데이터베이스 및 분할 맵 데이터베이스 액세스 권한이 있는지 확인합니다.  

* 탄력적 쿼리는 유효성을 검사하거나 외부 테이블에서 정의한 데이터 배포를 적용하지 않습니다. 실제 데이터 배포는 테이블 정의에 지정된 배포와 다른 경우 쿼리는 예기치 않은 결과를 얻을 수도 있습니다.

* 탄력적 쿼리는 처리되는 과정에서 특정 분할된 데이터베이스가 안전하게 제외될 수 있도록 조건자 분할키가 허락할 때 분할된 데이터베이스 제거를 수행하지 않습니다.

* 탄력적 쿼리는 분할된 데이터베이스에서 대부분의 계산을 수행할 수 있는 상황에서 가장 잘 실행됩니다. 일반적으로 분할된 데이터베이스 에서 평가될 수 있는 선택적인 필터 조건자를 가진 최상의 쿼리성능을 얻게 되거나 모든 분할 데이터베이스에서 파티션 정렬 방식으로 수행할 수 있는 분할 키를 조인하게 됩니다. 다른 쿼리 패턴은 분할된 데이터베이스에서 많은 양의 데이터를 로드해야 하기 때문에 성능이 좋지 않을 수 있습니다.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->

<!---HONumber=AcomDC_0413_2016-->