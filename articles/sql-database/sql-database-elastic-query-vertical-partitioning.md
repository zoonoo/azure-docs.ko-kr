<properties
    pageTitle="데이터베이스 간 쿼리를 위한 탄력적 데이터베이스 쿼리(수직 분할) | Microsoft Azure"
    description="수직 분할을 통해 데이터베이스 간 쿼리를 설정하는 방법"    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2015"
    ms.author="torsteng" />

# 데이터베이스 간 쿼리를 위한 탄력적 데이터베이스 쿼리(수직 분할)

이 문서에서는 데이터베이스 간 쿼리시나리오(수직 분할)에서의 탄력적 쿼리 설정 방법과 쿼리 수행 방법을 설명합니다. 수직 분할 시나리오의 정의는 [Azure SQL 탄력적 데이터베이스 쿼리 개요(미리 보기)](sql-database-elastic-query-overview.md)를 참조하세요.

## 데이터베이스 개체 만들기

수직 분할 시나리오에서 탄력적 쿼리는 원격 데이터베이스에 저장된 테이블을 참조하도록 현재 T-SQL DDL을 확장합니다. 이 섹션에서는 원격 테이블에 투명하게 액세스하기 위한 탄력적 쿼리를 구성하는 DDL 문의 개요를 제공합니다. 이 DDL 문을 사용하면 로컬 데이터베이스에서 원격 테이블의 메타데이터 표현을 만들 수 있습니다.

**참고**: 행 분할과 달리 이 DDL 문은 탄력적 데이터베이스 클라이언트 라이브러리를 통한 분할 맵이 있는 데이터 계층 정의에 종속되지 않습니다.

탄력적 데이터베이스 쿼리에 대한 데이터베이스 개체 정의는 아래의 수직 분할 시나리오를 상세히 설명하는 다음 T-SQL 문에 따릅니다.

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
    
또는 삭제하려면
    
    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
*< username>*에 *“@servername”* 접미사가 포함되지 않아야 합니다.

### 1\.2 외부 데이터 원본

외부 데이터 원본을 정의하여 탄력적 쿼리에 원격 데이터베이스 관련 정보를 제공합니다. 외부 데이터 원본을 생성 및 삭제하는 구문은 다음과 같이 정의 됩니다.

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

이 데이터 원본을 RDBMS로 정의하는 TYPE 매개 변수를 확인합니다.

외부 데이터 원본을 삭제 하려면 다음 문을 사용할 수 있습니다.

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

#### CREATE/DROP EXTERNAL DATA SOURCE 권한 

사용자는 모든 외부 데이터 원본 ALTER 권한이 있어야 합니다. 이 사용 권한은 ALTER DATABASE 권한에 포함 됩니다.

**예제**

다음 예제에서는 외부 데이터 원본에 대한 CREATE 문 사용을 보여줍니다.

	CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
	WITH 
	( 
		TYPE=RDBMS, 
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ReferenceData', 
		CREDENTIAL= SqlUser 
	); 
 
다음 카탈로그 뷰에서 현재 외부 데이터 원본의 목록을 검색하려면

    select * from sys.external_data_sources; 

### 1\.3 외부 테이블 

탄력적 쿼리는 기존 외부 테이블 구문을 확장하여 RDBMS 형식의 외부 데이터 원본을 사용하는 외부 테이블을 정의합니다. 수직 분할에 대한 외부 테이블 정의에서는 다음과 같은 측면을 다룹니다.

* **스키마**: 외부 테이블 DDL은 쿼리가 사용할 수 있는 스키마를 정의합니다. 외부 테이블 정의에서 제공한 스키마는 실제 데이터가 저장되는 원격 데이터베이스의 테이블 스키마와 일치해야 합니다. 

* **원격 데이터 참조**: 외부 테이블 DDL은 외부 데이터 원본을 가리킵니다. 외부 데이터 원본은 실제 테이블 데이터가 저장된 원격 데이터베이스의 논리적 서버 이름과 데이터베이스 이름을 지정합니다.

이전 섹션에서 설명한 대로 외부 데이터 소스를 사용하여 외부 테이블을 만드는 구문은 다음과 같습니다.

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
	{ WITH ( <rdbms_external_table_options> ) } 
	)[;] 
	
	<rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

DATA\_SOURCE 절은 외부 테이블에 사용하는 외부 데이터 원본(즉, 수직 분할의 경우 원격 데이터베이스)을 정의합니다.

SCHEMA\_NAME 및 OBJECT\_NAME 절은 각각 외부 테이블 정의를 원격 데이터베이스에 있는 다른 스키마 또는 다른 이름의 테이블에 매핑하는 기능을 제공합니다. 이 기능은 원격 데이터베이스에서 카탈로그 뷰나 DMV에 대해 외부 테이블을 정의하거나, 원격 테이블 이름을 이미 로컬로 사용하는 경우 등의 다른 상황에 유용합니다.

다음 DDL 문은 로컬 카탈로그에서 기존 외부 테이블 정의를 삭제합니다. 원격 데이터베이스에는 영향을 주지 않습니다.

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**CREATE/DROP EXTERNAL TABLE 권한**: 기본 데이터 원본을 참조하기 위해 외부 테이블 DDL에 대한 ALTER ANY EXTERNAL DATA SOURCE 권한도 필요합니다.

**보안 고려 사항:** 외부 테이블에 대한 액세스 권한이 있는 사용자는 외부 데이터 원본 정의에서 제공한 자격 증명에 따라 자동으로 기본 원격 테이블에 액세스할 수 있습니다. 외부 데이터 원본의 자격 증명을 통해 원치 않는 권한 상승을 방지하려면 외부 테이블에 대한 액세스 관리에 주의가 필요합니다. 일반 SQL 권한을 사용하여 일반 테이블에서처럼 외부 테이블에 대한 액세스를 부여하거나 취소할 수 있습니다.


 **예제**: 다음 예제에서는 외부 테이블을 만드는 방법을 보여 줍니다.

	CREATE EXTERNAL TABLE [dbo].[customer]( 
		[c_id] int NOT NULL, 
		[c_firstname] nvarchar(256) NULL, 
		[c_lastname] nvarchar(256) NOT NULL, 
		[street] nvarchar(256) NOT NULL, 
		[city] nvarchar(256) NOT NULL, 
		[state] nvarchar(20) NULL, 
		[country] nvarchar(50) NOT NULL, 
	) 
	WITH 
	( 
	       DATA_SOURCE = RemoteReferenceData 
	); 

다음 예제에서는 현재 데이터베이스에서 외부 테이블 목록을 검색 하는 방법을 보여줍니다.

	select * from sys.external_tables; 

## 쿼리

### 2\.1 최고 충실도 T-SQL 쿼리 

외부 데이터 원본 및 외부 테이블을 정의한 후 외부 테이블을 통해 전체 T-SQL을 사용할 수 있습니다.

**수직 분할 예**: 다음 쿼리는 주문 및 주문 라인을 위한 2개의 로컬 테이블과 고객을 위한 원격 테이블 간의 3방향 조인을 수행합니다. 탄력적 쿼리에 대한 참조 데이터 사용 사례의 예입니다.

	SELECT  	
	 c_id as customer,
	 c_lastname as customer_name,
	 count(*) as cnt_orderline, 
	 max(ol_quantity) as max_quantity,
	 avg(ol_amount) as avg_amount,
	 min(ol_delivery_d) as min_deliv_date
	FROM customer 
	JOIN orders 
	ON c_id = o_c_id
	JOIN  order_line 
	ON o_id = ol_o_id and o_c_id = ol_c_id
	WHERE c_id = 100

  
## 도구에 대한 연결

일반 SQL Server 연결 문자열을 사용하여, 탄력적 쿼리를 사용하도록 설정되었고 외부 테이블이 정의된 SQL DB 서버의 데이터베이스에 BI 및 데이터 통합 도구를 연결할 수 있습니다. SQL Server 도구에 대한 데이터 소스로 지원 되는지 확인 합니다. 그런 다음 도구와 연결하려는 타 SQL Server 데이터베이스와 똑같이 탄력적 쿼리 데이터베이스와 외부 테이블을 참조합니다.

## 모범 사례 
 
* SQL DB 방화벽 구성에서 Azure 서비스에 대한 액세스를 활성화하여 탄력적 쿼리 끝점 데이터베이스에 원격 데이터베이스 액세스 권한을 부여해야 합니다. 또한 외부 데이터 원본 종의에서 제공한 자격 증명으로 원격 데이터베이스에 로그인할 수 있고 원격 테이블에 대한 액세스 권한이 있는지 확인합니다.  

* 탄력적 쿼리는 원격 데이터베이스에서 대부분의 계산을 수행할 수 있는 상황에서 가장 잘 실행됩니다. 일반적으로 원격 데이터베이스에서 평가할 수 있는 선택적 필더 조건자나, 원격 데이터베이스에서 완전히 수행 가능한 조인을 통해 최고의 쿼리 성능을 얻을 수 있습니다. 다른 쿼리 패턴은 원격 데이터베이스에서 대규모의 데이터 로드가 필요할 수도 있기 때문에 성능이 좋지 않을 수 있습니다.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
<!--anchors-->

<!---HONumber=Oct15_HO4-->