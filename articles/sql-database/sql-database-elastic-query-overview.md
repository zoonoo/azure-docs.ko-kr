<properties
    pageTitle="Azure SQL 탄력적 데이터베이스 쿼리 미리보기"
    description="탄력적 쿼리 기능 개요"
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
    ms.date="07/09/2015"
    ms.author="sidneyh" />

# Azure SQL 탄력적 데이터베이스 쿼리 (미리보기) 개요.

**탄력적 데이터베이스 쿼리 기능**, 미리 보기에서 Azure SQL 데이터베이스에서 여러 데이터베이스에 걸쳐 있는 TRANSACT-SQL 쿼리를 실행할 수 있습니다. 이러한 데이터베이스는 특히 공통 스키마 (수평 분할 또는 분할이라고도 함)을 공유 하는 경우, Microsoft 및 타사 도구 (Excel, PowerBI, Tableau 등)와 여러 데이터베이스를 데이터 계층에 연결할 수 있습니다. 이 기능을 사용하여SQL 데이터베이스에서 큰 데이터 계층에 대한 쿼리를 확장할 수 있으며 비즈니스 인텔리전스 (BI) 보고서의 결과를 시각화할 수도 있습니다.

탄력적 데이터베이스 쿼리 응용 프로그램 작성을 시작 하려면 [탄력적 데이터베이스 쿼리 시작](sql-database-elastic-query-getting-started.md)을 참조하세요.

## 탄력적 데이터베이스 쿼리 시나리오

탄력적 데이터베이스 쿼리의 목표는 여러 데이터베이스를 통해 하나의 전체적인 결과를 얻을 수 있도록 보고 시나리오를 간소화하는 것입니다. 쿼리는 사용자 또는 응용 프로그램에서 직접 작성할 수도 있고 쿼리 데이터베이스에 연결된 도구를 통해 간접적으로 작성할 수도 있습니다. 이것은 보고서를 만들거나, 고급 BI 또는 데이터 통합도구- 혹은 변경할 수 없는 모든 소프트웨어를 사용할 때 특히 유용합니다. 탄력적 데이터베이스 쿼리와 함께 Excel, PowerBI, Tableau, 혹은 Cognos같은 친숙한 SQL 서버 안의 연결 환경 도구를 사용해 여러 데이터베이스를 쉽게 쿼리할 수 있습니다.

탄력적 데이터베이스 쿼리는 또한 SQL Server Management Studio 또는 Visual Studio에서 발급하는 쿼리를 통해 전체 데이터베이스 컬렉션에 쉽게 액세스할 수 있으며, Entity Framework 또는 기타 ORM 환경에서 데이터 베이스 간 쿼리를 수월하게 해줍니다. 그림 1 에서는 여기서는 확장된 데이터 계층 기반인 기존 클라우드 응용 프로그램 (탄력적 데이터베이스 도구 라이브러리를 사용하여)에서의 시나리오와 탄력적 데이터베이스 쿼리가 데이터베이스 간 보고에 사용되는 것을 보여줍니다.

**그림 1**

![확장 된 데이터 계층에서 사용 하는 탄력적 데이터베이스 쿼리][1]

데이터 계층은 공통 스키마를 사용하여 여러 데이터베이스에 걸쳐 확장됩니다. 이 방법은 수평 분할 또는 분할이라고도 합니다. 분할은 (1) [탄력적 데이터베이스 클라이언트 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)를 사용하여 수행되고 관리되며 (2) 여러 데이터베이스 간에 데이터를 배포하기 위해 응용 프로그램 관련 모델을 사용합니다. 이 토폴로지를 사용하면, 보고서는 다중 데이터베이스를 자주 포함합니다. 탄력적 데이터베이스 쿼리를 사용 하면, 이제 단일 SQL 데이터베이스에 연결할 수 있고 원격 데이터베이스에서의 쿼리 결과는 단일 가상 데이터베이스에서 생성하는 경우처럼 나타납니다.

> [AZURE.NOTE]탄력적 데이터베이스 쿼리는 대부분의 처리 데이터 계층에서 수행할 수 있는 부차적인 보고 시나리오에 가장 적합합니다. 쿼리가 보다 복잡한 과도한 보고 작업 부하 또는 데이터 웨어하우징 시나리오의 경우, [Azure SQL 데이터 웨어하우스](http://azure.microsoft.com/services/sql-data-warehouse/) 사용을 고려합니다.


## 탄력적 데이터베이스 쿼리 토폴로지

수평 분할 된 데이터 계층을 통해 보고 작업을 수행 하는 탄력적 데이터베이스 쿼리를 사용하는 탄력적인 확장 분할 된 데이터베이스 맵을 데이터 계층의 데이터베이스로 나타내는 데 필요 합니다. 일반적으로 이 시나리오에서는 하나의 분할 된 데이터베이스 맵은 시나리오에서 사용 되고 보고 쿼리에 대한 진입점으로 탄력적 데이터베이스 쿼리 기능을 사용하여 전용된 데이터베이스 역할을 합니다. 이 전용된 데이터베이스에만 아래 설명과 같이 탄력적 데이터베이스 쿼리 개체를 구성해야 합니다. 그림 2 에서는 이 토폴로지와 탄력적 데이터베이스 쿼리 데이터베이스 및 분할 된 데이터베이스 맵 사용하여 해당 구성을 보여줍니다.

> [AZURE.NOTE]전용 탄력적 데이터베이스 쿼리 데이터베이스는 SQL DB v12 데이터베이스이어야만 처음에는 프리미엄 계층만 지원됩니다. 자체 분할에 제한은 없습니다.

**그림 2**

![탄력적 데이터베이스 쿼리를 분할된 데이터베이스 계층에 대한 보고에 사용][2]

(**Shardlet**은 분할된 데이터베이스에서 분할 키의 단일 값과 연결된 모든 데이터입니다. **분할된 데이터베이스 키**:는 분할된 데이터베이스 간에 데이터를 배포하는 방법을 결정하는 열 값입니다. 예를 들어, 지역적으로 분포된 데이터는 분할 키로 지역 id를 가질 수 있습니다. 자세한 세부 정보는[탄력적인 확장 용어집](sql-database-elastic-scale-glossary.md))을 참조하세요.


시간이 지남에 따라 추가 토폴로지는 탄력적 데이터베이스 쿼리 기능을 통해 지원 됩니다. 이 문서는 새로운 기능이 추가되면 반영되도록 업데이트합니다.

## 분할 된 데이터베이스 맵을 구성 하 여 탄력적 쿼리를 사용 하도록 설정

탄력적 데이터베이스 쿼리 솔루션 생성은 탄력적 데이터베이스 쿼리로 원격 데이터베이스를 나타내는 데 탄력적 데이터베이스 도구 [**분할된 데이터베이스 맵**](sql-database-elastic-scale-shard-map-management.md)이 요구됩니다. 탄력적 데이터베이스 클라이언트 라이브러리를 이미 사용 중인 경우에 기존 분할 된 데이터베이스 맵을 사용할 수 있습니다. 그렇지 않은 경우 탄력적 데이터베이스 도구를 사용하여 분할된 데이터베이스 맵 생성이 필요합니다.

다음 예제 C\#code는 분할로 추가하는 단일원격 데이터베이스로 분할된 데이터베이스 맵을 생성하는 방법을 보여줍니다.

    ShardMapManagerFactory.CreateSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerCreateMode.ReplaceExisting, RetryBehavior.DefaultRetryBehavior);
    smm = ShardMapManagerFactory.GetSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerLoadPolicy.Lazy,
      RetryBehavior.DefaultRetryBehavior);
    map = smm.CreateRangeShardMap<int>("yourshardmapname");
    shard = map.CreateShard(new ShardLocation("yoursqldbserver", "yoursqldbdatabasename"));

분할된 데이터베이스 맵에 대한 자세한 내용은 [Shardmap 관리](sql-database-elastic-scale-shard-map-management.md)를 참조하세요.

탄력적 데이터베이스 쿼리 기능을 사용하기 위해서, 먼저 분할된 데이터베이스 맵을 생성하고 분할된 원격 데이터베이스를 등록해야합니다. 이 작업은 한 번만 수행하면 됩니다. 원격 데이터베이스-기존의 분할된 데이터베이스 맵에서 증분 작업-를 추가하거나 삭제할 때만 분할된 데이터베이스 맵을 변경해야합니다.


## 탄력적 데이터베이스 쿼리 데이터베이스 개체 만들기

탄력적 데이터베이스 쿼리 끝점에서 액세스할 수 있는 원격 테이블을 설명 하기 위해 응용 프로그램에 나타나는 외부테이블 타사 도구로 로컬테이블 됐었는지 정의하는 기능을 소개합니다. 도구를 통해 암시적으로 또는 명시적으로 SQL Server Management Studio, Visual Studio 데이터 도구에서 또는 응용 프로그램에서 이러한 데이터베이스 개체에 대한 쿼리를 제출할 수 있습니다. 탄력적 데이터베이스 쿼리는 외부 개체 안의 잠재력이 있는 원격 데이터베이스에 대해 처리 배포되는 쿼리의 중요한 차이점이 있는 다른 모든 Transact-SQL 문으로 실행합니다.

탄력적 데이터베이스 쿼리 기능은 4 개의 DDL 문을 사용합니다. 일반적으로 응용 프로그램 변경의 스키마가 변경될 때 DDL문이 사용될 경우는 한번 아니면 거의 없습니다.

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
*    [자격 증명 만들기](https://msdn.microsoft.com/library/ms189522.aspx)
*    [외부 데이터 원본 만들기/삭제](https://msdn.microsoft.com/library/dn935022.aspx)
*    [외부 테이블 만들기/삭제](https://msdn.microsoft.com/library/dn935021.aspx)

### 데이터베이스-범위 마스터 키 및 자격 증명

자격증명은 탄력적 데이터베이스 쿼리가 탄력적 확장 분할된 데이터베이스 맵과 Azure SQL DB의 원격 데이터베이스에 연결할 때 쓰이는 사용자 ID와 비밀번호를 나타냅니다. 다음 구문을 사용하여 필요한 마스터키 및 자격증명을 만들 수 있습니다.

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';  
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>
    WITH IDENTITY = '<shard_map_username>',
    SECRET = '<shard_map_password>'
     [;]
&lt;shard\_map\_username>이 모든@servername” suffix를 포함하지 않도록 확인하세요

자격 증명에 대한 정보는 sys.database\_scoped.credentials 카탈로그 뷰에 표시됩니다.

마스터 키 및 자격 증명을 삭제 하려면 다음 구문을 사용할 수 있습니다.

    DROP CREDENTIAL <credential_name> ON DATABASE;
    DROP MASTER KEY;  

### 외부 데이터 원본

다음 단계로, 분할 된 데이터베이스 맵을 외부 데이터 원본으로 등록 해야 합니다. 외부 데이터 원본을 생성 및 삭제하는 구문은 다음과 같이 정의 됩니다.

      CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
                     (TYPE = SHARD_MAP_MANAGER,
                     LOCATION = '<fully_qualified_server_name>',
                     DATABASE_NAME = '<shardmap_database_name>',
                     CREDENTIAL = <credential_name>,
                     SHARD_MAP_NAME = '<shardmapname>'
    ) [;]

외부 데이터 원본을 삭제 하려면 다음 문을 사용할 수 있습니다.

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

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

동일한 자격 증명은 분할된 데이터베이스 맵을 읽고 쿼리를 처리하는 동안 원격데이터베이스의 데이터에 액세스하는데 사용됩니다.


### 외부 테이블

탄력적 데이터베이스 쿼리를 사용하여 Azure SQL DB내의 분할된 원격 데이터베이스 간의 테이블에 참조할 수 있는 기존의 외부 테이블 구문을 확장할 수 있습니다. 위쪽의 외부데이터원본 개념을 사용하여, 만들 구문과 삭제할 외부테이블을 다음과 같이 정의합니다.

    CREATE EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    )[;]

    <sharded_external_table_options> ::=
          DATA_SOURCE = <External_Data_Source>,
          DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED | ROUND_ROBIN

분할 정책 테이블은 복제된 테이블 또는 분할된 데이터베이스 테이블로 간주되는지 여부를 제어합니다. 분할된 테이블을 사용하면, 데이터는 다른 분할된 데이터베이스와 겹치지 않습니다. 복제된 테이블은 모든 분할된 데이터베이스에 같은 데이터를 갖게 됩니다. 쿼리 프로세서는 정확 하 고 보다 효율적인 쿼리 처리를 위해 이 정보에 의존합니다. 라운드 로빈 배포 해당 테이블의 데이터 배포를 위해 응용 프로그램 특정 방법이 사용 됨을 나타냅니다.

    DROP EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name[;]

**외부 테이블 만들기/삭제**의 권한: ALTER ANY EXTERNAL DATA SOURCE 권한은 참조가 필요한 기본 데이터 원본이 필요합니다.

**예제**: 다음 예제에서는 외부 테이블을 만드는 방법을 보여 줍니다.

    CREATE EXTERNAL TABLE [dbo].[order_line](
        [ol_o_id] [int] NOT NULL,
        [ol_d_id] [tinyint] NOT NULL,
        [ol_w_id] [int] NOT NULL,
        [ol_number] [tinyint] NOT NULL,
        [ol_i_id] [int] NOT NULL,
        [ol_delivery_d] [datetime] NOT NULL,
        [ol_amount] [smallmoney] NOT NULL,
        [ol_supply_w_id] [int] NOT NULL,
        [ol_quantity] [smallint] NOT NULL,
        [ol_dist_info] [char](24) NOT NULL
    )
    WITH
    (
        DATA_SOURCE = MyExtSrc,
        DISTRIBUTION=SHARDED(ol_w_id)
    );

다음 예제에서는 현재 데이터베이스에서 외부 테이블 목록을 검색 하는 방법을 보여줍니다.

    select * from sys.external_tables;


## 보고 및 쿼리

### 쿼리
외부 데이터 원본 및 외부 테이블을 정의한 후 탄력적 데이터베이스 쿼리 기능을 사용 하는 데이터베이스에 연결할 친숙 한 SQL 데이터베이스 연결 문자열을 사용할 수 있습니다. 이제 외부 테이블을 통해 전체 읽기 전용 쿼리를 실행할 수 있습니다. 제한사항 일부는 아래에 있는[제한 사항 섹션](#preview-limitations)에서 설명합니다.

**예제**: 다음 쿼리는 웨어하우스, 주문 및 주문 라인 간의 3방향 조인을 수행하고 여러 집계 및 선택적인 필터를 사용합니다. 웨어하우스, 주문 및 주문 라인을 웨어하우스 id 열로 분할 됐다고 가정하면, 탄력적 데이터베이스 쿼리는 원격데이터베이스에 조인을 배치할 수 있고 쿼리의 비용이 많이 드는 부분의 처리를 확장 할수 있습니다.

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

### Stored procedure SP\_ EXECUTE\_FANOUT

SP\_EXECUTE\_FANOUT은 분할 된 데이터베이스 맵을 나타내는 데이터베이스의 액세스를 제공 하는 저장된 프로시저입니다. 다음 매개 변수를 사용 하는 저장된 프로시저:

-    **서버 이름**(nvarchar): 분할된 데이터베이스 맵에 호스트된 논리 서버의 정규화된 이름입니다.
-    **분할 맵 데이터베이스 이름**(nvarchar): 분할된 데이터베이스 맵 데이터베이스의 이름입니다.
-    **사용자 이름**(nvarchar): 분할된 데이터베이스 맵 및 원격 데이터베이스에 로그인하기 위한 사용자 이름입니다.
-    **암호**(nvarchar): 사용자 암호입니다.
-    **분할된 데이터베이스 맵 이름**(nvarchar): 쿼리에 대해 사용할 분할된 데이터베이스 맵 이름입니다.
-    **쿼리**: 쿼리를 각 분할된 데이터베이스에서 실행할 수 있습니다.

호출 매개 변수에서 제공 하는 분할 된 데이터베이스 맵을 사용하여 등록 된 모든 분할 된 데이터베이스 맵에 지정된 문을 실행할 수 있습니다. 모든 결과는 다중-분할된 데이터베이스 쿼리와 비슷한UNION ALL 의미체계를 사용하여 병합됩니다. 결과는 원격 데이터베이스 이름으로 추가된 '가상' 열이 포함 됩니다.

동일한 자격증명은 분할 맵 데이터 베이스와 분할된 데이터베이스를 연결하는데 사용된다는 점을 명심하세요

**예제:**

    sp_execute_fanout 'myserver.database.windows.net', N'ShardMapDb', N'myuser', N'MyPwd', N'ShardMap', N'select count(w_id) as foo from warehouse'

## 도구에 대한 연결
BI 및 데이터 통합 도구를 연결 하 여 탄력적 데이터베이스 쿼리 데이터베이스를 친숙 한 SQL DB 연결 문자열로 사용할 수 있습니다. SQL Server 도구에 대한 데이터 소스로 지원 되는지 확인 합니다. 그런 다음 도구와 연결할 수 있는 다른 SQL서버와 같은 탄력적 데이터베이스 쿼리데이터베이스의 외부개체를 사용합니다.

## 모범 사례
*    분할 된 데이터베이스 맵 관리자 데이터베이스와 데이터베이스들이 방화벽 규칙안의 MIcrosoft Azure에 액세스를 허용하는 분할된 데이터베이스 맵으로 정의 되었는지 확인합니다. 탄력적 데이터베이스 쿼리 데이터베이스가 그것들에 연결할 수 있도록 이 작업이 필요합니다. 자세한 내용은 [Azure SQL DB 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx)을 참조하세요.
*    탄력적 데이터베이스 쿼리는 유효성을 검사하거나 외부테이블에 의해 정의된 데이터 분포를 적용하지 않습니다. 실제 데이터 배포는 테이블 정의에 지정된 배포와 다른 경우 쿼리는 예기치 않은 결과를 얻을 수도 있습니다.
*    탄력적 데이터베이스 쿼리를 사용 하는 가장 적합 한 쿼리는 분할된 데이터베이스에서 수행할 수 있는 대부분의 계산에서 작동됩니다. 일반적으로 분할된 데이터베이스 에서 평가될 수 있는 선택적인 필터 조건자를 가진 최상의 쿼리성능을 얻게 되거나 모든 분할 데이터베이스에서 파티션 정렬 방식으로 수행할 수 있는 분할 키를 조인하게 됩니다. 다른 쿼리 패턴 헤드 노드는 분할 된 데이터베이스에서 많은 양의 데이터를 로드해야 하기 때문에 성능이 좋지 않을 수 있습니다.

## 비용

탄력적 데이터베이스 쿼리는 Azure SQL 데이터베이스가 포함 됩니다. 참고 토폴로지는 원격 데이터베이스가 탄력적 데이터베이스 쿼리 끝점보다 다른 데이터 중앙에 있을 때 지원되지만, 원격 데이터베이스에서 데이터 송신은 일반 Azuure 송신요금이 부과됩니다.

## 미리 보기 제한 사항

미리 보기를 할 때 몇가지 염두에 두어야 할 것이 있습니다.

*    탄력적 데이터베이스 쿼리기능은 처음에 SQL DB V12프리미엄 성능 계층에서만 사용 가능하지만, 탄력적 데이터베이스 쿼리에 의해 액세스되는 원격 데이터베이스는 모든 계층에서 사용 가능합니다.
* 외부 데이터 원본 참조 하는 외부 테이블만 있는 원격 데이터베이스에 대해 읽기 작업을 지원 합니다. 그러나 외부테이블 정의 자체에 상주하는 탄력적 데이터베이스 쿼리 데이터베이스의 전체 Transact-SQL기능을 가리키도록 설정할 수 있습니다. 예를 들어, SELECT column\_list INTO local\_table을 사용하여 나온 임시적인 결과를 유지하거나 외부테이블을 참조하는 탄력적 데이터베이스 쿼리 데이터베이스의 저장 프로시져를 정의할 때 유용합니다.
*    원격 데이터베이스에 현재 쿼리의 매개 변수를 전달할 수 없습니다. 매개 변수가 있는 쿼리는 모든 데이터를 헤드노드에 가져와야 하고 데이터 크기에 따라 성능이 저하될 수 있습니다. 임시 Workround는 쿼리내의 매개변수를 방지하거나 자동으로 해당 현재값으로 대체하는 매개변수를 가질 수 있게 RECOMPILE 옵션을 사용합니다.
* 외부테이블에 대한 열 수준 통계는 현재 지원되지 않습니다.
* 탄력적 데이터베이스 쿼리는 처리되는 과정에서 특정 원격 데이터베이스가 안전하게 제외될 수 있도록 조건자 분할키가 허락할 때 분할된 데이터베이스 제거를 수행하지 않습니다. 결과적으로, 쿼리는 항상 쿼리의 외부 데이터 원본에 의해 표현되는 모든 원격 데이터베이스를 touch합니다.
* 다른 데이터베이스의 테이블간의 조인에 관련된 모든 쿼리는 결과 성능 비용과 탄력적 데이터베이스 쿼리 데이터베이스 처리를 위해 많은 수의 행을 가져옵니다. 각 원격 데이터베이스에서 로컬로 처리 될 수 있는 쿼리를 개발 하거나 조인을 수행 하기 전에 각 데이터베이스에서 관련 행을 제한 하는 절 위치를 사용하는 것이 가장 좋습니다.
*    탄력적 데이터베이스 쿼리 메타데이터 정의에 의해 사용되는 구문은 미리보기 중 변경됩니다.
*    SSMS 또는 SSDT의 Transact-SQL 스크립팅 기능은 현재 탄력적 데이터베이스 쿼리 개체와 작동되지 않습니다.

## 사용자 의견
Disqus 또는 Stackoverflow 경험에 대한 의견을 공유 하십시오. 모든 종류의 서비스에 대한 사용자 의견에 관심이 있습니다.(결함, 조잡함, 기능의 격차)

## 다음 단계
탄력적 데이터베이스 쿼리 탐색을 시작하려면 [탄력적 데이터베이스 쿼리 시작](sql-database-elastic-query-getting-started.md) 단계별 자습서에서 몇 분안에 끝낼 수 있는 전체 작업 예제들을 시도해 봅니다.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png

<!--anchors-->

<!---HONumber=August15_HO8-->