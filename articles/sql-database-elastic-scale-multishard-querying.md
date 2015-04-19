<properties 
	pageTitle="다중 분할된 데이터베이스 쿼리" 
	description="탄력적인 확장 API를 사용하여 분할된 데이터베이스 간에 쿼리를 실행합니다." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="torsteng@microsoft.com"/>

# 다중 분할된 데이터베이스 쿼리
## 개요
**다중 분할된 데이터베이스 쿼리**는 여러 분할된 데이터베이스 간에 걸친 쿼리를 실행해야 하는 데이터 컬렉션/보고와 같은 작업에 사용됩니다. 이 기능을 단일 분할된 데이터베이스에서 모든 작업을 수행하는 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)과비교할 수 있습니다. 

탄력적인 확장 클라이언트 라이브러리에서는 **Microsoft.Azure.SqlDatabase.ElasticScale.Query**라는 새 네임스페이스를 도입하며, 이 네임스페이스는 단일 쿼리 및 결과를 사용하여 여러 분할된 데이터베이스를 쿼리하는 기능을 제공합니다. 분할된 데이터베이스의 컬렉션에 대해 쿼리 추상화를 제공합니다. 또한 대체 실행 정책, 특히 부분 결과를 제공하여 많은 분할된 데이터베이스를 쿼리할 때 오류를 처리합니다.  

다중 분할된 데이터베이스 쿼리의 주 진입점은 **MultiShardConnection** 클래스입니다. 데이터 종속 라우팅의 경우와 마찬가지로 API는 **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)** 클래스 및 메서드의 친숙한 환경을 따릅니다. **SqlClient** 라이브러리를 사용할 경우 첫 번째 단계에서는 **SqlConnection**을 만든 다음 연결용 **SqlCommand**를 만들고 **Execute** 메서드 중 하나를 통해 명령을 실행합니다. 그리고 마지막으로 **SqlDataReader**는 명령 실행에서 반환된 결과 집합을 반복합니다. 다중 분할된 데이터베이스 쿼리 API 경험은 다음 단계를 따릅니다. 

1. **MultiShardConnection**을 만듭니다.
2. **MultiShardConnection**용 **MultiShardCommand**를 만듭니다.
3. 명령을 실행합니다.
4. **MultiShardDataReader**를 통해 결과를 사용합니다. 

주요 차이점은 다중 분할된 데이터베이스 연결을 생성한다는 것입니다. 여기서 **SqlConnection**은 단일 데이터베이스에서 작동하고, **MultiShardConnection**은 ***분할된 데이터베이스의 컬렉션***을 입력으로 사용합니다. 분할된 데이터베이스 맵에서 분할된 데이터베이스의 컬렉션을 채울 수 있습니다. 그런 다음 쿼리는 **UNION ALL** 의미 체계를 사용하여 분할된 데이터베이스의 컬렉션에서 실행되어 전반적인 단일 결과를 어셈블리합니다. 필요에 따라 행이 속한 분할된 데이터베이스의 이름을 명령에서 **ExecutionOptions** 속성을 사용하여 출력에 추가할 수 있습니다. 다음 코드는 *myShardMap*이라는 **ShardMap**을 사용하는 다중 분할된 데이터베이스 쿼리를 보여 줍니다. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
            	{ 
                	while (sdr.Read())
                    	{ 
                        	var c1Field = sdr.GetString(0); 
                        	var c2Field = sdr.GetFieldValue<int>(1); 
                        	var c3Field = sdr.GetFieldValue<Int64>(2);
                    	} 
             	} 
           } 
    } 
 

**myShardMap.GetShards()** 호출을 살펴보세요. 이 메서드는 분할된 데이터베이스 맵에서 모든 분할된 데이터베이스를 검색하고 해당 분할된 데이터베이스 맵의 모든 분할된 데이터베이스 간에 쿼리를 실행하는 간편한 방법을 제공합니다. 다중 분할된 데이터베이스 쿼리에 대한 분할된 데이터베이스의 컬렉션은 **myShardMap.GetShards()** 호출에서 반환된 컬렉션에 대해 LINQ 쿼리를 수행하여 구체화할 수 있습니다. 부분 결과 정책과 함께, 다중 분할된 데이터베이스 쿼리의 현재 기능은 수십에서 수백 개의 분할된 데이터베이스에 대해 원활하게 작동하도록 설계되었습니다.
다중 분할된 데이터베이스 쿼리의 제한 사항은 현재 쿼리가 실행되는 shardlet 및 분할된 데이터베이스에 대한 유효성 검사가 없다는 것입니다. 데이터 종속 라우팅이 쿼리 시 지정된 분할된 데이터베이스가 분할된 데이터베이스 맵의 일부인지 확인하며, 다중 분할된 데이터베이스 쿼리가 이 검사를 수행하지는 않습니다. 따라서 이후에 분할된 데이터베이스 맵에서 제거된 분할된 데이터베이스에서 다중 분할된 데이터베이스 쿼리가 실행될 수 있습니다.

## 다중 분할된 데이터베이스 쿼리 및 분할/병합 작업

다중 분할된 데이터베이스 쿼리는 쿼리되는 분할된 데이터베이스의 shardlet이 지속적인 분할/병합 작업에 참여하는지 여부를 확인하지 않습니다. 따라서 동일한 shardlet의 행이 동일한 다중 분할된 데이터베이스 쿼리의 여러 분할된 데이터베이스에 대해 표시하는 경우 불일치가 발생할 수 있습니다. 이러한 제한 사항에 유의하고 다중 분할된 데이터베이스 쿼리를 수행하는 동안 사용되는 지속적인 분할/병합 작업과 분할된 데이터베이스 맵 변경 사항을 고려해야 합니다.

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
