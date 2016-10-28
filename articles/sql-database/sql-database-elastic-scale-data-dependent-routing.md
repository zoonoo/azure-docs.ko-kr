<properties 
	pageTitle="데이터 종속 라우팅 | Microsoft Azure" 
	description="Azure SQL 데이터베이스의 탄력적 데이터베이스 기능인 데이터 종속 라우팅을 위해 .NET 앱에서 ShardMapManager 클래스를 사용하는 방법" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2016" 
	ms.author="torsteng"/>

#데이터 종속 라우팅

**데이터 종속 라우팅**은 쿼리에서 데이터를 사용하여 적절한 데이터베이스로 요청을 라우트하는 기능입니다. 이러한 방식은 분할된 데이터베이스에서 작업할 때의 기본 패턴입니다. 요청 컨텍스트는 특히 분할 키가 쿼리의 일부가 아닌 경우 요청을 라우트하는 데 사용될 수도 있습니다. 데이터 종속 라우팅을 사용하는 응용 프로그램의 구체적인 각 쿼리 또는 트랜잭션은 요청당 단일 데이터베이스에 대한 액세스로 제한됩니다. Azure SQL 데이터베이스 탄력적 데이터베이스 도구의 경우 이 라우팅은 ADO.NET 응용 프로그램의 **[ShardMapManager 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)**와 함께 수행됩니다.

응용 프로그램에서는 다양한 연결 문자열 또는 분할된 환경의 여러 데이터 조각과 연결된 DB 위치를 추적하지 않아도 됩니다. 대신 [분할된 데이터베이스 맵 관리자](sql-database-elastic-scale-shard-map-management.md)에서 필요한 경우 분할된 데이터베이스 맵의 데이터 및 응용 프로그램의 요청 대상인 분할 키의 값에 따라 올바른 데이터베이스에 연결을 엽니다. 이 키는 일반적으로 *customer\_id*, *tenant\_id*, *date\_key* 또는 데이터베이스 요청의 기본 매개 변수인 다른 특정 식별자입니다.

자세한 내용은 [Scaling Out SQL Server with Data Dependent Routing](https://technet.microsoft.com/library/cc966448.aspx)(데이터 종속 라우팅을 사용하여 SQL Server 크기 조정)을 참조하세요.

## 클라이언트 라이브러리 다운로드

클래스를 가져오려면 [탄력적 데이터베이스 클라이언트 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)를 설치하세요.

## 데이터 종속 라우팅 응용 프로그램에서 ShardMapManager 사용 

응용 프로그램은 초기화 중 팩터리 호출 **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**를 사용하여 **ShardMapManager**를 인스턴스화해야 합니다. 이 예제에서는 **ShardMapManager**및 특정 **ShardMap**이 모두 초기화됩니다. 이 예제에서는 GetSqlShardMapManager 및 [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx) 메서드를 보여 줍니다.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### 가능한 가장 낮은 권한 자격 증명을 사용하여 분할된 데이터베이스 맵 가져오기

응용 프로그램에서 분할된 데이터베이스 맵 자체를 조작하지 않는 경우 팩터리 메서드에서 사용되는 자격 증명은 **전역 분할된 데이터베이스 맵** 데이터베이스에서 읽기 전용 권한만 갖습니다. 이러한 자격 증명을 분할된 데이터베이스 맵 관리자에 대한 연결을 여는데 사용되는 자격 증명과는 일반적으로 다릅니다. 또는 [탄력적 데이터베이스 클라이언트 라이브러리 액세스에 사용되는 자격 증명](sql-database-elastic-scale-manage-credentials.md)을 참조하세요.

## OpenConnectionForKey 메서드 호출

**[ShardMap.OpenConnectionForKey 메서드](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)**는 **key** 매개 변수 값을 기반으로 적합한 데이터베이스에 명령을 실행할 수 있는 ADO.Net 연결을 반환합니다. 분할된 데이터베이스 정보는 **ShardMapManager**를 통해 응용 프로그램에 캐시되므로, 이러한 요청 시에는 일반적으로 **전역 분할된 데이터베이스 맵**에 대한 데이터베이스 조회를 수행하지 않습니다.

	// Syntax: 
	public SqlConnection OpenConnectionForKey<TKey>(
		TKey key,
		string connectionString,
		ConnectionOptions options
	)


* **key** 매개 변수는 요청에 대한 적합한 데이터베이스를 결정하는 분할된 데이터베이스 맵의 조회 키로 사용됩니다.

* **connectionString**은 원하는 연결에 대한 사용자 자격 증명만 전달하는 데 사용됩니다. 메서드에서 **ShardMap**을 사용하여 데이터베이스 및 서버를 결정하므로 데이터베이스 이름 또는 서버 이름은 이 *connectionString*에 포함되지 않습니다.

* 분할된 데이터베이스 맵이 변경되고 분할 또는 병합 작업의 결과로 행이 다른 데이터베이스로 이동될 수 있는 환경에서는 **[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)**를 **ConnectionOptions.Validate**로 설정해야 합니다. 이는 연결이 응용 프로그램에 전달되기 전 대상 데이터베이스의 로컬 분할된 데이터베이스 맵(전역의 분할된 데이터베이스 맵이 아님)에 대한 간략한 쿼리와 관련됩니다.

로컬 분할된 데이터베이스 맵에 대한 유효성 검사가 실패하면(캐시가 올바르지 않음을 나타냄) 분할된 데이터베이스 맵 관리자가 조회를 위한 올바른 새 값을 가져오고, 캐시를 업데이트하고, 적절한 데이터베이스 연결을 반환합니다.

**ConnectionOptions.None**은 응용 프로그램이 온라인 상태이며 분할된 데이터베이스 매핑 변경이 필요하지 않는 경우에만 사용합니다. 이 경우 캐시된 값은 항상 올바른 것으로 간주되며, 대상 데이터베이스에 대한 추가 왕복 유효성 검사 호출을 건너뛸 수 있습니다. 이렇게 하면 데이터베이스 트래픽이 감소합니다. **connectionOptions**는 구성 파일의 값을 통해 특정 기간 동안 분할 변경이 필요한지 여부를 나타내도록 설정될 수 있습니다.

이 예제에서는 **customerShardMap**이라는 **ShardMap** 개체를 사용하는 정수 키 **CustomerID** 값을 사용합니다.

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
	// constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

**OpenConnectionForKey** 메서드는 이미 열려 있는 새 연결을 올바른 데이터베이스에 반환합니다. 이러한 방식으로 활용되는 연결은 ADO.Net 연결 풀링을 완벽하게 활용합니다. 트랜잭션 및 요청을 한 번에 하나의 분할된 데이터베이스를 통해 충족할 수 있다면 이미 ADO.Net을 사용하는 응용 프로그램에서는 이 수정만 수행하면 됩니다.

응용 프로그램에서 ADO.Net과 함께 비동기 프로그래밍을 사용하는 경우 **[OpenConnectionForKeyAsync 메서드](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)**도 사용할 수 있습니다. 이 메서드의 동작은 ADO.Net의 **[Connection.OpenAsync](https://msdn.microsoft.com/library/hh223688(v=vs.110).aspx)** 메서드에 해당하는 데이터 종속 라우팅입니다.

## 일시적인 오류 처리와 통합 

클라우드에서 데이터 액세스 응용 프로그램을 개발할 경우 일시적인 오류는 앱을 통해 catch하고 오류를 throw하기 전에 작업이 여러 번 다시 시도되는지 확인하는 것이 가장 좋습니다. 클라우드 응용 프로그램에 대한 일시적인 오류 처리는 [일시적인 오류 처리](https://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx))에서 설명합니다.
 
일시적인 오류 처리는 데이터 종속 라우팅 패턴과 함께 자연스럽게 사용할 수 있습니다. 주요 요구 사항은 데이터 종속 라우팅 연결을 가져온 **using** 블록을 포함하여 전체 데이터 액세스 요청을 다시 시도하는 것입니다. 위의 예제를 다음과 같이 다시 작성할 수 있습니다(강조 표시된 변경 참조).

### 예제 - 일시적인 오류 처리를 사용하는 데이터 종속 라우팅 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() = > </span> 
<span style="background-color:  #FFFF00">    { </span>
        // 고객 ID에 대한 분할된 데이터베이스에 연결합니다. 
        사용하여(SqlConnection conn = customerShardMap.OpenConnectionForKey (customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            //간단한 명령 실행 
            SqlCommand cmd conn =CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            집합 PersonID @newPersonID = 
                            WHERE CustomerID = @customerID "; 

            cmd. Parameters.AddWithValue ("@customerID", customerId); 
            cmd. Parameters.AddWithValue ("@newPersonID", newPersonId); 
            cmd. Executenonquery (); 

            Console.WriteLine ("업데이트 완료"); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


일시적인 오류 처리를 구현하는 데 필요한 패키지는 탄력적 데이터베이스 샘플 응용 프로그램을 빌드할 때 자동으로 다운로드됩니다. [엔터프라이즈 라이브러리 - 일시적인 오류 처리 응용 프로그램 블록](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)에서도 패키지가 별도로 제공됩니다. 버전 6.0 이상을 사용하세요.

## 트랜잭션 일관성 

트랜잭션 속성은 분할된 데이터베이스에 로컬인 모든 작업에 대해 보장됩니다. 예를 들어 데이터 종속 라우팅을 통해 제출된 트랜잭션은 연결의 대상인 분할된 데이터베이스 범위 내에서 실행합니다. 이때 트랜잭션으로 여러 연결을 등록하기 위한 기능은 제공되지 않으므로 분할된 데이터베이스 간에 수행되는 작업에 대한 트랜잭션은 보장되지 않습니다.

## 다음 단계
분할된 데이터베이스를 분리하거나 다시 연결하려면 [RecoveryManager 클래스를 사용하여 분할된 데이터베이스 맵 문제 해결](sql-database-elastic-database-recovery-manager.md)을 참조하세요.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0706_2016-->