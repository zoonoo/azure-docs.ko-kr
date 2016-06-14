<properties 
	pageTitle="탄력적 데이터베이스 도구 및 행 수준 보안을 제공하는 다중 테넌트 응용 프로그램" 
	description="탄력적 데이터베이스 도구와 행 수준 보안을 함께 사용하여 다중 테넌트 분할된 데이터베이스를 지원하고 Azure SQL 데이터베이스의 데이터 계층을 유연하게 확장할 수 있는 응용 프로그램 구축 방법에 대해 알아보세요." 
	metaKeywords="azure sql database elastic tools multi tenant row level security rls" 
	services="sql-database" 
    documentationCenter=""  
	manager="jhubbard" 
	authors="tmullaney"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="thmullan;torsteng" />

# 탄력적 데이터베이스 도구 및 행 수준 보안을 제공하는 다중 테넌트 응용 프로그램 

[탄력적 데이터베이스 도구](sql-database-elastic-scale-get-started.md) 및 [RLS(행 수준 보안)](https://msdn.microsoft.com/library/dn765131)는 Azure SQL 데이터베이스로 다중 테넌트 응용 프로그램의 데이터를 유연하고 효율적으로 확장할 수 있는 강력한 기능 집합을 제공합니다. 이 문서에서는 이러한 기술을 함께 사용하여 **ADO.NET SqlClient** 및/또는 **Entity Framework**를 통해 다중 테넌트 분할된 데이터베이스를 지원하는 우수한 확장성의 응용 프로그램을 구축하는 방법을 보여 줍니다.

* **탄력적 데이터베이스 도구**를 사용하면 개발자는 .NET 라이브러리 및 Azure 서비스 템플릿 집합을 사용하는 산업 표준 분할 관행을 통해 응용 프로그램의 데이터 계층을 확장할 수 있습니다. 탄력적 데이터베이스 클라이언트 라이브러리를 사용하여 분할된 데이터베이스를 관리하면 일반적으로 분할과 관련된 여러 인프라 작업을 자동화 및 간소화하는 데 도움이 됩니다. 

* **행 수준 보안**을 사용하면 개발자는 쿼리를 실행하는 테넌트에 속하지 않은 행을 필터링하는 보안 정책을 사용하여 여러 테넌트의 데이터를 같은 데이터베이스에 저장할 수 있습니다. 액세스 논리를 응용 프로그램이 아닌 데이터베이스 내부에 중앙 집중화하면 코드 베이스가 성장하기 때문에 응용 프로그램의 유지 관리가 간소화되고 오류 위험이 완화됩니다. RLS를 사용하려면 최신 [Azure SQL 데이터베이스 업데이트(V12)](sql-database-v12-whats-new.md)가 필요합니다.

이러한 기능을 함께 사용하면 여러 테넌트의 데이터를 같은 분할된 데이터베이스에 저장하여 응용 프로그램의 비용을 절감하고 효율성을 높일 수 있습니다. 또한 다중 테넌트 분할된 데이터베이스는 테넌트 간에 리소스가 균등하게 분산된다는 보장이 없기 때문에 보다 엄격한 성능 보장을 필요로 하는 "프리미엄" 테넌트를 위해 격리된 단일 테넌트 분할된 데이터베이스를 제공하는 응용 프로그램의 유연성은 그대로 유지됩니다.

간단히 말해서 탄력적 데이터베이스 클라이언트 라이브러리의 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md) API는 테넌트를 해당 분할 키(일반적으로 "TenantId")가 포함된 올바른 분할된 데이터베이스에 자동으로 연결합니다. 데이터베이스에 연결되면 데이터베이스의 RLS 보안 정책에 따라 테넌트는 해당 TenantId가 포함된 행에만 액세스할 수 있습니다. 모든 테이블에는 각 테넌트에 속한 행을 나타내는 TenantId 열이 포함되어 있는 것으로 추정됩니다.

![앱 아키텍처 블로그][1]

## 샘플 프로젝트 다운로드

### 필수 조건
* Visual Studio 2012 이상 사용 
* Azure SQL 데이터베이스 3개 생성 
* 샘플 프로젝트 다운로드: [Azure SQL을 위한 탄력적 DB 도구 - 다중 테넌트 분할된 데이터베이스](http://go.microsoft.com/?linkid=9888163)
  * **Program.cs** 시작 부분에서 데이터베이스에 대한 정보 입력 

이 프로젝트는 [Azure SQL을 위한 탄력적 DB 도구 - Entity Framework 통합](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)에서 설명한 프로젝트에 다중 테넌트 분할된 데이터베이스에 대한 지원을 추가하는 확장 프로젝트입니다. 위의 다이어그램에서 볼 수 있듯이 테넌트 네 명과 다중 테넌트 분할 데이터베이스 두 개를 사용하여 블로그 및 게시물을 만드는 간단한 콘솔 응용 프로그램을 구축하는 프로젝트입니다.

응용 프로그램을 빌드 및 실행합니다. 그러면 탄력적 데이터베이스 도구의 분할된 데이터베이스 맵 관리자가 부트스트랩되고 다음 테스트가 실행됩니다.

1. Entity Framework 및 LINQ를 사용하여 새 블로그를 만든 후 각 테넌트에 대한 모든 블로그 표시
2. ADO.NET SqlClient를 사용하여 특정 테넌트에 대한 모든 블로그 표시
3. 잘못된 테넌트에 대한 블로그 삽입을 시도하여 오류가 throw되는지 확인  

분할된 데이터베이스에서 아직 RLS를 설정하지 않았기 때문에 각 테스트에서 다음 문제가 발생합니다. 테넌트가 자신에게 속하지 않은 블로그를 볼 수 있으며 응용 프로그램에서 잘못된 테넌트에 대한 블로그를 삽입할 수 있습니다. 이 문서의 나머지 부분에서는 RLS로 테넌트를 강제 격리하여 이러한 문제를 해결하는 방법을 설명합니다. 두 단계가 있습니다.

1. **응용 프로그램 계층**: 연결을 연 후 응용 프로그램 코드를 수정하여 SESSION\_CONTEXT에서 현재 TenantId를 항상 설정합니다. 샘플 프로젝트는 이 작업이 이미 완료되었습니다. 
2. **데이터 계층**: 각 분할된 데이터베이스에서 SESSION\_CONTEXT에 저장된 TenantId에 따라 행을 필터링하는 RLS 보안 정책을 만듭니다. 분할된 데이터베이스 각각에 대해 이 작업을 수행해야 합니다. 그렇지 않으면 다중 테넌트 분할된 데이터베이스의 행이 필터링되지 않습니다. 


## 1단계) 응용 프로그램 계층: SESSION\_CONTEXT에서 TenantId 설정

탄력적 데이터베이스 클라이언트 라이브러리의 데이터 종속 라우팅 API를 사용하여 분할된 데이터베이스에 연결된 후에도 RLS 보안 정책에서 다른 테넌트에 속한 행을 필터링할 수 있도록 응용 프로그램에서는 어떤 TenantId가 해당 연결을 사용하고 있는지 데이터베이스에 알려야 합니다. 이 정보를 전달하는 권장 방법은 [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx)에서 해당 연결의 현재 TenantId를 저장하는 것입니다. (참고: [CONTEXT\_INFO](https://msdn.microsoft.com/library/ms180125.aspx)도 사용할 수 있지만 사용하기 쉬우며 기본적으로 NULL을 반환하고 키-값 쌍을 지원하는 SESSION\_CONTEXT가 더 나은 옵션입니다.)

### Entity Framework

Entity Framework를 사용하는 응용 프로그램의 경우 가장 간단한 방법은 [EF DbContext를 사용하는 데이터 종속 라우팅](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext)에 설명된 ElasticScaleContext 재정의 내에서 SESSION\_CONTEXT를 설정하는 것입니다. 데이터 종속 라우팅을 통해 조정된 연결을 반환하기 전에 SESSION\_CONTEXT의 'TenantId'를 해당 연결에 대해 지정된 shardingKey로 설정하는 SqlCommand를 만들어서 실행하면 됩니다. 이 방법을 사용하면 SESSION\_CONTEXT를 설정하는 코드를 한 번만 작성하면 됩니다.

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

이제 ElasticScaleContext가 호출될 때마다 SESSION\_CONTEXT는 자동으로 지정된 TenantId로 설정됩니다.

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
	{     
		var query = from b in db.Blogs
	                orderby b.Name
	                select b;
		
		Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
		foreach (var item in query)     
		{       
			Console.WriteLine(item.Name);     
		}   
	} 
}); 
```

### ADO.NET SqlClient 

ADO.NET SqlClient를 사용하는 응용 프로그램에 권장되는 방법은 ShardMap.OpenConnectionForKey() 주위에 래퍼 함수를 생성하는 것입니다. ShardMap.OpenConnectionForKey()는 연결이 반환되기 전에 SESSION\_CONTEXT의 'TenantId'를 올바른 Tenantld로 자동적으로 설정해줍니다. SESSION\_CONTEXT가 항상 설정되도록 하기 위해 이 래퍼 함수를 사용해서만 연결을 열어야 합니다.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## 2단계) 데이터 계층: 행 수준 보안 정책 만들기

### 각 테넌트가 액세스할 수 있는 행을 필터링하는 보안 정책 만들기

응용 프로그램에서 SESSION\_CONTEXT를 현재 TenantId로 설정한 후 쿼리하므로 RLS 보안 정책에서 쿼리를 필터링하고 TenantId가 다른 행을 제외할 수 있습니다.

RLS는 T-SQL에서 구현됩니다. 사용자 정의 함수에서 액세스 논리를 정의하고, 보안 정책에서 이 함수를 모든 테이블에 바인딩합니다. 이 프로젝트의 경우 이 함수는 다른 SQL 사용자가 아닌 응용 프로그램이 데이터베이스에 연결되었는지, SESSION\_CONTEXT에 저장된 'TenantId'가 지정된 행의 TenantId와 일치하는지만 확인합니다. 필터 조건자는 이러한 조건을 충족하는 행이 SELECT, UPDATE 및 DELETE 쿼리에 대한 필터를 통과하도록 하며 차단 조건자는 이러한 조건을 위반하는 행이 INSERT 또는 UPDATE되지 않도록 합니다. SESSION\_CONTEXT가 설정되지 않은 경우 NULL을 반환하고 행이 표시되지 않거나 삽입할 수 없습니다.

RLS를 사용하려면 Visual Studio(SSDT), SSMS 또는 프로젝트에 포함된 PowerShell 스크립트 중 하나를 사용하여 모든 분할된 데이터베이스에서 다음 T-SQL을 실행합니다. 또는 [Elastic Database Jobs](sql-database-elastic-jobs-overview.md)를 사용하는 경우 모든 분할된 데이터베이스에서 이 T-SQL을 자동으로 실행할 수 있습니다.

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
	RETURNS TABLE     
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS fn_accessResult          
		WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
		AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP] 수백 개의 테이블에서 조건자를 추가해야 하는 복잡한 프로젝트의 경우 자동으로 보안 정책을 생성하여 스키마의 모든 테이블에 조건자를 추가하는 도우미 저장 프로시저를 사용할 수 있습니다. [모든 테이블에 행 수준 보안 적용 – 도우미 스크립트(블로그)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script)를 참조하세요.

이제 샘플 응용 프로그램을 다시 실행하면 각 테넌트는 자신에게 속한 행만 볼 수 있습니다. 또한 응용 프로그램에서는 현재 분할된 데이터베이스에 연결된 테넌트 이외의 다른 테넌트에 속한 행을 삽입할 수 없으며 표시되는 행을 다른 TenantId를 포함하도록 업데이트할 수 없습니다. 응용 프로그램에서 두 작업을 시도하면 DbUpdateException이 발생합니다.

나중에 새 테이블을 추가하는 경우 보안 정책을 변경하고 새 테이블에 필터 및 차단 조건자를 추가하기만 하면 됩니다.

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### 삽입 시 TenantId를 자동으로 채우도록 기본 제약 조건 추가 

행을 삽입할 때 자동으로 SESSION\_CONTEXT에 현재 저장된 값으로 TenantId를 채우도록 각 테이블에 기본 제약 조건을 추가할 수 있습니다. 예:

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
	ADD CONSTRAINT df_TenantId_Blogs      
	DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
	ADD CONSTRAINT df_TenantId_Posts      
	DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

이제 행을 삽입할 때 응용 프로그램에서 TenantId를 지정할 필요가 없습니다.

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
	{
		var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
		db.Blogs.Add(blog);     
		db.SaveChanges();   
	} 
}); 
```

> [AZURE.NOTE] Entity Framework 프로젝트에 기본 제약 조건을 사용하는 경우 EF 데이터 모델에 TenantId 열을 포함하지 않는 것이 좋습니다. Entity Framework 쿼리에서 자동으로 기본값을 제공하는데, 이 기본값은 T-SQL에서 만든 SESSION\_CONTEXT를 사용하는 기본 제약 조건을 재정의하기 때문입니다. 예를 들어 샘플 프로젝트에서 기본 제약 조건을 사용하려면 DataClasses.cs에서 TenantId를 제거하고(그리고 패키지 관리자 콘솔에서 Add-Migration을 실행하고) 데이터베이스 테이블에만 필드가 있도록 T-SQL을 사용해야 합니다. 그러면 데이터 삽입 시 EF에서 잘못된 기본값을 자동으로 제공하지 않습니다.

### (선택 사항) 모든 행에 액세스 하려면 "superuser"를 사용 하도록 설정
예를 들면 일부 응용 프로그램은 모든shard안의 모든 tenant가 보고할 수 있게 하거나 데이터베이스간의 이동 테넌트 행에 관련된 shard에서 분할/합병 작업을 수행하기 위해 모든 행에 액세스할 수 있는 “SuperUser”생성을 요구합니다. 이 기능을 사용 하려면 각 분할 데이터베이스에서 새 SQL 사용자 (이 예에서 "superuser")를 만들어야 합니다. 그런 다음 사용자를 모든 행에 액세스할 수 있도록 하는 새 조건자 함수를 사용하여 보안 정책을 변경 합니다.

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### 유지 관리 

* **새로운 분할된 데이터베이스 추가**: T-SQL 스크립트를 실행하여 모든 새로운 분할된 데이터베이스에서 RLS를 설정해야 합니다. 그렇지 않으면 분할된 데이터베이스에 대한 쿼리가 필터링되지 않습니다.

* **새 테이블 추가**: 새 테이블을 만들 때마다 모든 분할된 데이터베이스의 보안 정책에 필터 및 차단 조건자를 추가해야 합니다. 그렇지 않으면 새 테이블에 대한 쿼리가 필터링되지 않습니다. [새로 만든 테이블에 자동으로 행 수준 보안 적용(블로그)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx)에 설명된 것처럼 DDL 트리거를 사용하여 이 작업을 자동화할 수 있습니다.


## 요약 

탄력적 데이터베이스 도구와 행 수준 보안을 함께 사용하면 다중 테넌트 및 단일 테넌트 분할된 데이터베이스를 모두 지원하여 응용 프로그램의 데이터 계층을 확장할 수 있습니다. 다중 테넌트 분할된 데이터베이스를 사용하여 데이터를 보다 효율적으로 저장할 수 있고(특히 테넌트 수는 많은데 데이터 행은 적은 경우), 단일 테넌트 분할된 데이터베이스를 사용하여 성능 및 격리 요구 사항이 엄격한 프리미엄 테넌트를 지원할 수 있습니다. 자세한 내용은 [행 수준 보안 참조](https://msdn.microsoft.com/library/dn765131)를 참조하세요.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->

 

<!---HONumber=AcomDC_0601_2016-->