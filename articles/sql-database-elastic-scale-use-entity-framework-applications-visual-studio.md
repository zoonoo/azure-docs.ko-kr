<properties title="Using Elastic Scale with Entity Framework" pageTitle="Entity Framework와 함께 탄력적인 확장 사용" description="탄력적인 확장은 확장을 용이하게 하고, Entity Framework는 데이터베이스를 코딩하는 데 쉽게 사용할 수 있습니다. " metaKeywords="Using Elastic Scale with Entity Framework, Azure SQL Database sharding, elastic scale, Entity Framework and Elastic Scale" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Entity Framework와 함께 탄력적인 확장 사용 
 
Microsoft의 EF(Entity Framework)와 함께 Azure SQL 데이터베이스의 탄력적인 확장을 사용하여 응용 프로그램을 빌드할 수 있습니다. 탄력적인 확장을 사용하면 응용 프로그램의 데이터 계층에 대한 분할 및 확장을 통해 용량을 확장 및 축소할 수 있습니다. 이 문서에서는 탄력적인 확장 기능을 통합하는 데 필요한 Entity Framework 응용 프로그램의 변경 내용을 보여 줍니다. 여기서는 [탄력적인 확장의 분할된 데이터베이스 관리](http://go.microsoft.com/?linkid=9862595) 및 [데이터 종속 라우팅](./sql-database-elastic-scale-data-dependent-routing.md)(Entity Framework **Code First** 접근 방식 사용)을 중점적으로 다룹니다. EF에 대한 [Code First - 새 데이터베이스](http://msdn.microsoft.com/ko-kr/data/jj193542.aspx)자습서가 이 문서 전체에서 예제를 실행하는 역할을 합니다. 이 문서와 함께 제공되는 샘플 코드는 Visual Studio 코드 샘플의 탄력적인 확장 샘플 중 일부입니다.
  
## 샘플 코드 다운로드 및 실행
이 기사의 코드를 다운로드하려면:

* Visual Studio 2012 이상이 필요합니다. 
* Visual Studio를 시작합니다. 
* Visual Studio에서 파일 -> 새 프로젝트를 선택합니다. 
* '새 프로젝트' 대화 상자에서 **Visual C#**용 **온라인 샘플**로 이동하고 상단 오른쪽에 있는 검색 상자에 "탄력적인 확장"을 입력합니다.
    
    ![Entity Framework and elastic scale sample app][1] 

A    **Azure SQL 데이터베이스를 사용하는 탄력적인 확장 - Entity Framework와 통합**이라는 샘플을 선택합니다. 라이선스를 수락한 후 샘플을 로드합니다. 

샘플을 실행하려면 Azure SQL 데이터베이스에서 3개의 빈 데이터베이스를 만들어야 합니다.

* 분할된 데이터베이스 맵 관리자 데이터베이스
* 분할 1 데이터베이스
* 분할 2 데이터베이스

이러한 데이터베이스를 만든 후에는 Azure SQL DB 서버 이름, 데이터베이스 이름 및 데이터베이스에 연결하는 데 사용하는 자격 증명으로 **Program.cs**의 자리 표시자를 채웁니다. Visual Studio에서 솔루션을 빌드합니다. Visual Studio는 탄력적인 확장, Entity Framework 및 일시적인 오류 처리를 위해 필요한 NuGet 패키지를 빌드 프로세스 중에 다운로드합니다. 사용 중인 솔루션에 대해 NuGet 패키지를 복원할 수 있는지 확인합니다. Visual Studio 솔루션 탐색기에서 솔루션 파일을 마우스 오른쪽 단추로 클릭하여 이 설정을 사용할 수 있습니다. 

##Entity Framework 워크플로 

Entity Framework 개발자는 다음 4개의 워크플로 중 하나를 사용하여 응용 프로그램을 구축하고 응용 프로그램 개체의 지속성을 확인합니다. 

* **Code First(새 데이터베이스)**: EF 개발자가 응용 프로그램 코드에서 모델을 만들면 EF가 해당 모델에서 데이터베이스를 생성합니다. 
* **Code First(기존 데이터베이스)**: 개발자가 EF를 통해 기존 데이터베이스에서 모델에 대한 응용 프로그램 코드를 생성할 수 있습니다.
* **Model First**: 개발자가 EF 디자이너에서 모델을 만들면 EF가 해당 모델에서 데이터베이스를 만듭니다.
* **Database First**: 개발자가 EF 도구를 사용하여 기존 데이터베이스에서 모델을 유추합니다. 

이러한 모든 접근 방법은 DbContext 클래스를 사용하여 응용 프로그램용 데이터베이스 연결 및 데이터베이스 스키마를 쉽게 이해할 수 있게 관리합니다. 이 문서의 뒷부분에서 더 자세히 다루겠지만 DbContext 기본 클래스의 여러 생성자는 연결 생성, 데이터베이스 부트스트래핑 및 스키마 생성에 대해 다양한 수준의 제어를 허용합니다. EF에서 제공하는 데이터베이스 연결 관리가 Azure 데이터베이스의 탄력적인 확장에서 제공하는 데이터 종속 라우팅 인터페이스의 연결 관리 기능과 교차한다는 사실에서 주로 문제가 발생합니다. 

##탄력적인 확장 가정 

용어 정의에 대해서는 [탄력적인 확장 용어집](./sql-database-elastic-scale-glossary.md)을 참조하세요.

Azure SQL 데이터베이스의 탄력적인 확장을 사용하여 shardlet이라고 하는 응용 프로그램 데이터의 파티션을 정의합니다.  Shardlet은 분할 키로 식별되며 특정 데이터베이스에 매핑됩니다. 응용 프로그램은 필요한 만큼 많은 데이터베이스를 포함하고 shardlet을 배포하여 현재 비즈니스 요구 사항에 따른 용량이나 성능을 충분히 제공할 수 있습니다. 탄력적인 확장 API에서 제공하는 분할된 데이터베이스 맵을 통해 데이터베이스에 대한 분할 키 값의 매핑이 저장됩니다. 이 기능을 분할된 데이터베이스 맵 관리 또는 줄여서 SMM이라고 합니다. 분할된 데이터베이스 맵은 분할 키를 전송하는 요청에 대한 데이터베이스 연결의 브로커 역할도 합니다. 이 기능을 데이터 종속 라우팅이라고 합니다. 
 
탄력적인 확장의 분할된 데이터베이스 맵 관리자는 동시 shardlet 관리 작업(예: 분할된 데이터베이스 간에 데이터 재배치)이 수행될 때 발생할 수 있는 shardlet 데이터에 대한 불일치한 뷰가 생성되지 않도록 합니다. 이를 위해, 탄력적인 확장의 분할된 데이터베이스 맵이 탄력적인 확장 응용 프로그램용 데이터베이스 연결을 조정합니다. 따라서 분할된 데이터베이스 관리 작업에서 연결이 생성된 shardlet 영향을 줄 수 있는 경우 분할된 데이터베이스 맵 기능이 자동으로 데이터베이스 연결을 중지합니다. 이 접근 방식은 EF의 기능 중 일부(예: 기존 연결에서 새 연결을 생성하여 데이터베이스 존재 여부 확인)와 통합해야 합니다. 일반적으로 표준 DbContext 생성자는 EF 작업을 위해 안전하게 복제할 수 있는 닫힌 데이터베이스 연결에 대해서만 안정적으로 작동합니다. 탄력적인 확장의 설계 원칙에서만 열린 연결을 조정합니다. 탄력적인 확장을 통해 조정된 연결을 EF DbContext로 전달하기 전에 닫으면 이 문제를 해결할 수 있습니다. 그러나 연결을 닫고 EF를 사용하여 다시 열면 탄력적인 확장을 통해 수행된 유효성 검사 및 일관성 검사가 사라집니다. 그러나 EF의 마이그레이션 기능은 이러한 연결을 사용하여 응용 프로그램 작동에 영향을 주지 않는 방식으로 기본 데이터베이스 스키마를 관리합니다. 동일한 응용 프로그램에서 탄력적인 확장 및 EF의 이러한 기능을 모두 유지하고 결합하는 것이 좋습니다. 다음 섹션에서는 이러한 속성 및 요구 사항을 자세히 설명합니다. 


##요구 사항 

탄력적인 확장 및 Entity Framework API 모두에서 작업할 때 보통 다음 속성을 유지하려고 합니다. 

* **확장**: 응용 프로그램의 용량 요구 사항에 따라 필요한 만큼 분할된 응용 프로그램의 데이터 계층에서 데이터베이스를 추가 또는 제거합니다. 즉, 데이터베이스의 생성 및 삭제와 탄력적인 확장의 분할된 데이터베이스 맵 관리자 API 아키텍처 사용을 제어하여 데이터베이스 및 shardlet의 매핑을 관리한다는 의미입니다. 

* **일관성**: 응용 프로그램은 분할을 사용하며, 탄력적인 확장의 데이터 종속 라우팅 기능을 사용합니다. 손상되었거나 잘못된 쿼리 결과를 방지하기 위해 탄력적인 확장의 분할된 데이터베이스 맵 관리자를 통해 연결이 조정됩니다. 또한 유효성 검사 및 일관성도 유지됩니다.
 
* **Code First**: EF의 Code First 패러다임의 편의성을 유지합니다. Code First에서 응용 프로그램의 클래스는 기본 데이터베이스 구조에 매핑됩니다. 응용 프로그램 코드는 기본 데이터베이스 처리와 관련된 대부분의 측면을 마스크하는 DbSet를 조작합니다.
 
* **스키마**: Entity Framework는 초기 데이터베이스 스키마 생성과 마이그레이션을 통한 후속 스키마 전개를 처리합니다. 이러한 기능을 유지하면 데이터가 전개될 때 그에 맞춰 응용 프로그램을 쉽게 조정할 수 있습니다. 

다음 지침에서는 Azure SQL 데이터베이스의 탄력적인 확장을 사용하여 Code First 응용 프로그램에 대한 이러한 요구 사항을 충족하는 방법을 제공합니다. 

## EF DbContext를 사용하는 데이터 종속 라우팅 

Entity Framework에 대한 데이터베이스 연결은 일반적으로 **DbContext**의 서브클래스를 통해 관리합니다. **DbContext**에서 파생하여 이러한 서브클래스를 만듭니다. 여기서 사용 중인 응용 프로그램용 CLR 개체의 데이터베이스 기반 컬렉션을 구현하는 **DbSets**를 정의할 수 있습니다. 데이터 종속 라우팅의 컨텍스트에서 다른 EF Code First 응용 프로그램 시나리오의 경우에는 유지하지 않아도 되는 몇 가지 유용한 속성을 확인할 수 있습니다. 

* 데이터베이스가 이미 존재하고 탄력적인 확장의 분할된 데이터베이스 맵에 등록되어 있습니다. 
* 응용 프로그램의 스키마가 아래에 설명된 데이터베이스에 이미 배포되었습니다. 
* 데이터베이스에 대한 데이터 종속 라우팅 연결이 탄력적인 확장의 분할된 데이터베이스 맵을 통해 조정됩니다. 

확장을 위한 데이터 종속 라우팅에**DbContext**를 통합하려면:

1. 분할된 데이터베이스 맵 관리자의 탄력적인 확장 인터페이스를 통해 실제 데이터베이스 연결을 만듭니다. 
2. 연결을 **DbContext** 서브클래스로 래핑합니다.
3. 연결을 **DbContext** 기본 클래스로 전달하여 EF 쪽의 모든 처리가 적절히 수행되는지 확인합니다. 

다음 코드 예제에서 이 접근 방식을 보여 줍니다. 이 코드는 함께 제공되는 Visual Studio 프로젝트에도 있습니다.

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    ...

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

#### 요점
* 새로운 생성자가 DbContext 서브클래스의 기본 생성자를 대체합니다. 
* 새로운 생성자는 탄력적인 확장을 통해 데이터 종속 라우팅에 필요한 다음과 같은 인수를 사용합니다. 
    * 데이터 종속 라우팅 인터페이스에 액세스하는 분할된 데이터베이스 맵 
    * shardlet을 식별하는 분할 키 
    * 분할된 데이터베이스에 대한 데이터 종속 라우팅 연결에 필요한 자격 증명 및 연결 문자열 
* 기본 클래스 생성자에 대한 호출은 데이터 종속 라우팅에 필요한 모든 단계를 수행하는 정적 메서드로 우회합니다. 
    * 이는 분할된 데이터베이스 맵에서 탄력적인 확장 인터페이스의 **OpenConnectionForKey** 호출을 사용하여 열린 연결을 설정합니다. 
    * 분할된 데이터베이스 맵은 지정된 분할 키용 shardlet을 유지하는 분할된 데이터베이스에 대해 열린 연결을 만듭니다.
    * 이 열린 연결은 **DbContext**의 기본 클래스 생성자로 전달되어 EF를 통해 새 연결을 자동으로 만드는 대신 이 연결이 EF에서 사용되도록 지정합니다. 이러한 방식으로 분할된 데이터베이스 맵 관리 작업에서 일관성이 보장되도록 탄력적인 확장에서 연결 태그를 지정합니다.  
  
코드에서 기본 생성자 대신 DbContext 서브클래스의 새로운 생성자를 사용합니다. 다음은 예제입니다. 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     ... 
    }

새로운 생성자는 **tenantid1**의 값으로 식별되는 shardlet용 데이터를 유지하는 분할된 데이터베이스에 대한 연결을 엽니다. **Using** 블록의 코드는 **tenantid1**용 분할된 데이터베이스에서 EF를 사용하는 블로그용 **DbSet**에 계속 액세스합니다. 그에 따라 모든 데이터베이스 작업의 범위가 이제 **tenantid1**이 유지되는 단일 분할된 데이터베이스로 제한되도록 Using 블록의 코드 의미 체계가 변경됩니다. 예를 들어, 블로그 **DbSet**에 대한 LINQ 쿼리는 다른 분할된 데이터베이스에 저장된 블로그가 아닌 현재 분할된 데이터베이스에 저장된 블로그만 반환합니다.  

####일시적인 오류 처리
Microsoft Patterns & Practices 팀에서 [일시적인 오류 처리 응용 프로그램 블록](http://msdn.microsoft.com/ko-kr/library/dn440719(v=pandp.60).aspx)(영문)을 게시했습니다. 이 라이브러리는 EF와 함께 탄력적인 확장에 사용됩니다. 그러나 모든 일시적인 예외가 특정 위치 즉, 일시적인 오류 후 조정된 생성자를 사용하여 연결이 시도되도록 새 생성자가 사용되는지 확인할 수 있는 위치로 반환되어야 합니다. 그렇지 않으면 올바른 분할된 데이터베이스에 대한 연결을 보장되지 않으며, 분할된 데이터베이스 맵이 변경될 때 연결이 유지되지 않을 수 있습니다. 

다음 코드 샘플에서는 새 **DbContext** 서브클래스 생성자를 중심으로 SQL 재시도 정책을 사용할 수 있는 방법을 보여줍니다. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            ... 
            } 
        }); 

위의 코드에서 **SqlDatabaseUtils.SqlRetryPolicy**는 10번의 재시도와 재시도 간 5초의 대기 시간을 사용하는 **SqlDatabaseTransientErrorDetectionStrategy**로 정의됩니다. 이 접근 방식은 EF 및 사용자가 시작한 트랜잭션에 대한 지침과 일치합니다. [재시도 실행 전략 제한 사항(EF6 이상)](http://msdn.microsoft.com/ko-kr/data/dn307226)(영문)을 참조하세요. 두 경우 모두 응용 프로그램에서 일시적인 예외가 반환하는 범위를 제어해야 합니다. 그래야 트랜잭션을 다시 열거나 그림과 같이 탄력적인 확장 라이브러리를 사용하는 적절한 생성자에서 컨텍스트를 다시 만들 수 있습니다.  

위의 코드 예제는 응용 프로그램에서 Entity framework와 함께 탄력적인 확장의 데이터 종속 라우팅을 사용하는 데 필요한 기본 생성자 다시 작성 방법을 보여 줍니다. 다음 표에서는 다른 생성자에 대한 이 접근 방식을 일반화합니다. 


현재 생성자 | 데이터용 생성자 다시 작성 | 기본 생성자 | 참고 사항
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |연결은 분할된 데이터베이스 맵 및 데이터 종속 라우팅 키의 한 기능이어야 합니다. EF를 통한 자동 연결 생성을 무시하고 분할된 데이터베이스 맵을 사용하여 연결을 조정해야 합니다. 
MyContext (string) |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |연결은 분할된 데이터베이스 맵 및 데이터 종속 라우팅 키의 한 기능이어야 합니다. 고정 데이터베이스 이름 또는 연결 문자열은 분할된 데이터베이스 맵에 의한 유효성 검사를 무시하므로 작동하지 않습니다. 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |지정된 분할된 데이터베이스 맵 및 분할 키에 대한 연결이 제공된 모델을 사용하여 생성됩니다. 컴파일된 모델이 기본 c'tor로 전달됩니다.
MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, bool) |DbContext (DbConnection, bool) |연결은 분할된 데이터베이스 맵 및 키에서 유추되어야 합니다. 연결은 입력으로 제공할 수 없습니다(해당 입력이 이미 분할된 데이터베이스 맵 및 키를 사용하고 있지 않다면). 부울이 전달됩니다. 
MyContext (string, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, bool) |연결은 분할된 데이터베이스 맵 및 키에서 유추되어야 합니다. 연결은 입력으로 제공할 수 없습니다(해당 입력이 분할된 데이터베이스 맵 및 키를 사용하고 있지 않다면). 컴파일된 모델이 전달됩니다. 
MyContext (bool ObjectContext) |ElasticScaleContext (ShardMap TKey, ObjectContext, bool) |DbContext (bool ObjectContext) |새로운 생성자가 입력으로 전달된 ObjectContext의 모든 연결이 탄력적인 확장을 통해 관리되는 연결로 다시 라우팅되는지 확인해야 합니다. ObjectContexts에 대한 자세한 설명은 이 문서의 범위를 벗어납니다.
MyContext (DbConnection, DbCompiledModel, bool) |ElasticScaleContext (ShardMap TKey, DbCompiledModel, bool) | DbContext (DbConnection, DbCompiledModel, bool); |연결은 분할된 데이터베이스 맵 및 키에서 유추되어야 합니다. 연결은 입력으로 제공할 수 없습니다(해당 입력이 이미 분할된 데이터베이스 맵 및 키를 사용하고 있지 않다면). 모델 및 Boolean이 기본 클래스 생성자에 전달됩니다. 

###EF 마이그레이션을 통해 분할된 데이터베이스 스키마 배포 

자동 스키마 관리는 Entity Framework에서 제공하는 편리한 기능입니다. 탄력적인 확장 응용 프로그램의 컨텍스트에서, 데이터베이스가 분할된 응용 프로그램에 추가될 때 새로 생성된 분할된 데이터베이스에 스키마를 자동으로 프로 비전하는 이 기능을 유지하려고 합니다. 기본 사용 사례는 EF를 사용하여 분할된 응용 프로그램에 대한 데이터 계층의 용량을 늘리는 것입니다. 스키마 관리에 EF의 기능을 사용하면 EF를 기반으로 하는 분할된 응용 프로그램에 대한 데이터베이스 관리 작업이 줄어듭니다. 

EF 마이그레이션을 통한 스키마 배포는 **열지 않은 연결**에 가장 적합합니다. 이 시나리오는 탄력적인 확장에서 제공하는 열린 연결을 사용하는 데이터 종속 라우팅 시나리오와 대조적입니다. 다른 차이점은 일관성 요구 사항입니다. 동시 분할된 데이터베이스 맵 조작을 방지하기 위해 모든 데이터 종속 라우팅 연결에 대해 일관성을 유지하는 것이 바람직하긴 하지만 아직 분할된 데이터베이스 맵에 등록되지 않았고 shardlet을 유지하도록 아직 할당되지 않은 새 데이터베이스에 초기 스키마를 배포할 경우에는 문제가 없습니다. 따라서 데이터 종속 라우팅과 달리 이 시나리오에 대한 일반 데이터베이스 연결을 사용할 수 있습니다.  

그 결과, 이 접근 방식에서는 EF 마이그레이션을 통한 스키마 배포는 새 데이터베이스를 응용 프로그램의 분할된 데이터베이스 맵에 분할된 데이터베이스로 등록하는 과정과 밀접하게 결합됩니다. 다음 필수 구성 요소가 충족되어야 합니다. 

* 데이터베이스가 이미 생성되었습니다. 
* 데이터베이스가 비어 있습니다. 즉, 사용자 스키마와 사용자 데이터가 없습니다.
* 데이터 종속 라우팅을 위한 탄력적인 확장 API를 통해 데이터베이스에 아직 액세스할 수 없습니다. 

이러한 필수 구성 요소가 적절히 충족되면 열지 않은 일반 **SqlConnection**을 만들어 스키마 배포에 대 한 EF 마이그레이션을 시작할 수 있습니다. 다음 코드 샘플에서 이 접근 방법을 보여 줍니다. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

이 샘플에서는 **RegisterNewShard** 메서드를 보여 줍니다. 이 메서드는 탄력적인 확장 분할된 데이터베이스 맵에 분할된 데이터베이스를 등록하고, EF 마이그레이션을 통해 스키마를 배포하고, 분할된 데이터베이스에 대한 분할 매핑을 저장합니다. 이 샘플은 SQL 연결 문자열을 입력으로 사용하는 **DbContext** 서브클래스(샘플에서는 **ElasticScaleContext**)의 생성자를 사용합니다. 이 생성자의 코드는 다음 예제와 같이 간단합니다. 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
기본 클래스에서 상속된 생성자의 버전을 사용할 수도 있습니다. 그러나 이 코드는 연결할 때 EF의 기본 이니셜라이저가 사용되는지 확인해야 합니다. 따라서 연결 문자열을 사용하여 기본 클래스 생성자를 호출하기 전에 정적 메서드로 짧게 우회합니다. EF에 대한 이니셜라이저 설정이 충돌하지 않도록 분할된 데이터베이스의 등록은 다른 앱 도메인 또는 프로세스에서 실행해야 합니다. 


##제한 사항 

이 문서에서 설명하는 접근 방식에는 몇 가지 제한 사항이 따릅니다. 

* **LocalDb**를 사용하는 EF 응용 프로그램은 탄력적인 확장을 사용하기 전에 먼저 일반 SQL Server 데이터베이스를 마이그레이션해야 합니다. **LocalDb**의 경우 탄력적인 확장을 사용하는 분할된 데이터베이스를 통해서 응용 프로그램을 확장할 수 없습니다. 개발 단계에서는 계속 **LocalDb**를 사용할 수 있습니다. 

* 데이터베이스 스키마 변경을 내재하는 응용 프로그램에 대한 모든 변경 내용의 경우 모든 분할된 데이터베이스에서 EF 마이그레이션을 거쳐야 합니다. 이 문서의 샘플 코드에서는 이 작업을 수행하는 방법을 보여 주지 않습니다. ConnectionString 매개 변수와 함께 Update-Database를 사용하여 모든 분할된 데이터베이스에 대해 반복하거나, -Script 옵션과 함께 Update-Database를 사용하여 보류 중인 마이그레이션에 대한 T-SQL 스크립트를 추출하여 사용 중인 분할된 데이터베이스에 적용하는 것이 좋습니다.  

* 요청이 있는 경우 모든 데이터베이스 처리가 요청에 지정된 분할 키로 식별되는 단일 분할된 데이터베이스에 포함된다고 간주됩니다. 그러나 이 가정이 항상 참인 것은 아닙니다. 예를 들어 분할 키를 사용 가능하도록 설정하지 못할 수 있습니다. 이 문제를 해결하기 위해 탄력적인 확장 라이브러리는 **MultiShardQuery** 클래스를 제공합니다. 이 클래스는 여러 분할된 데이터베이스에 대해 쿼리할 수 있도록 연결 추상화를 구현합니다. EF와 함께 **MultiShardQuery**를 사용하는 방법은 이 문서의 범위를 벗어납니다.



##결론 

Entity Framework 응용 프로그램은 Azure SQL 데이터베이스의 탄력적인 확장에서 여러 이점을 쉽게 활용할 수 있습니다. 이 문서에 설명된 단계를 통해 EF 응용 프로그램은 EF 응용 프로그램에 사용된 **DbContext** 서브클래스의 생성자를 리팩터링하여 데이터 종속 라우팅을 위한 탄력적인 확장 기능을 사용할 수 있습니다. 따라서 **DbContext** 클래스가 이미 존재하는 위치에서만 변경해야 합니다. 또한 EF 응용 프로그램은 탄력적인 확장의 분할된 데이터베이스 맵에 새 분할된 데이터베이스 및 매핑을 등록하는 단계와 필요한 EF 마이그레이션을 호출하는 단계를 결합하여 자동 스키마 배포의 이점을 계속 활용할 수 있습니다. 


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-using-entity-framework/sample.png
