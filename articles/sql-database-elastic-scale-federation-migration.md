<properties title="Federations Migration" pageTitle="페더레이션 마이그레이션" description="Outlines the steps to migrate an existing app built with Federations feature to Elastic Scale model." metaKeywords="sharding scaling, federations, Azure SQL DB sharding, Elastic Scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

#페더레이션 마이그레이션 

Azure SQL 데이터베이스 페더레이션 기능이 2015년 9월의 Web/Business Edition과 함께 사용 중지됩니다. 사용 중지되면 페더레이션 기능을 활용하는 응용 프로그램의 실행이 중단됩니다. 성공적인 마이그레이션을 위해서는, 가능한 한 빨리 마이그레이션 작업을 시작하여 충분한 계획 및 실행을 허용하는 것이 좋습니다. 이 문서는 현재 페더레이션 응용 프로그램을 [Azure SQL DB 탄력적인 확장 Preview API](http://go.microsoft.com/?linkid=9862592)로 마이그레이션하는 방법을 보여 주는 컨텍스트, 예제 및 페더레이션 마이그레이션 유틸리티에 대한 소개를 제공합니다. 이 문서의 목적은 데이터를 이동하지 않고 페더레이션 응용 프로그램을 마이그레이션할 수 있는 제안된 단계를 안내합니다.

탄력적인 확장 API를 사용하는 응용 프로그램으로 기존 페더레이션 응용 프로그램을 마이그레이션하는 주요 단계는 세 가지로 구성됩니다.

1. [페더레이션 루트에서 분할된 데이터베이스 맵 관리자 만들기] 
2. [기존 응용 프로그램 수정]
3. [기존 페더레이션 멤버 외부 전환]
    

### 마이그레이션 샘플 도구
이 프로세스를 지원하기 위해 [페더레이션 마이그레이션 유틸리티](http://go.microsoft.com/?linkid=9862613)를 만들었습니다. 이 유틸리티가 1 - 3 단계를 수행합니다.

## <a name="create-shard-map-manager"></a>페더레이션 루트에서 분할된 데이터베이스 맵 관리자 만들기
페더레이션 응용 프로그램 마이그레이션의 첫 번째 단계는 분할된 데이터베이스 맵 관리자의 구문에는 페더레이션 루트 메타데이터를 복제하는 것입니다. 

![Clone the federation root to the shard map manager][1]
 
테스트 환경에서 기존 페더레이션 응용 프로그램으로 시작합니다.
 
탄력적 확장 [분할된 데이터베이스 맵 관리자](http://go.microsoft.com/?linkid=9862595)의 구문에 페더레이션 루트 메타데이터를 복제하려면 **페더레이션 마이그레이션 유틸리티**를 사용합니다. 페더레이션 루트와 유사한 분할된 데이터베이스 맵 관리자 데이터베이스가 분할된 데이터베이스 맵(즉, 페더레이션), 분할된 데이터베이스(즉, 페더레이션 멤버) 및 각 범위 매핑에 대한 참조를 포함하는 독립 실행형 데이터베이스입니다. 

분할된 데이터베이스 맵 관리자에 페더레이션 루트를 복제하면 메타데이터가 복사 및 변환됩니다. 페더레이션 루트에서는 메타데이터가 변경되지 않습니다. 페더레이션 마이그레이션 유틸리티와 페더레이션 루트를 복제하는 것은 지정 시점 작업이며 분할된 데이터베이스 맵 또는 페더레이션 루트에 대한 변경 내용은 다른 각 데이터 저장소에 반영되지 않습니다. 새로운 API를 테스트하는 동안 페더레이션 루트를 변경하는 경우 페더레이션 마이그레이션 유틸리티를 사용하여 현재 상태를 나타내는 분할된 데이터베이스 맵을 새로 고칠 수 있습니다. 

![Migrate the existing app to use the Elastic Scale APIs][2]

## 기존 응용 프로그램 수정 

적절한 분할된 데이터베이스 맵 관리자와 분할된 데이터베이스 맵 관리자에 등록된 페더레이션 멤버 및 범위(마이그레이션 유틸리티를 통해)을 사용하여, 탄력적인 확장 API를 활용하도록 기존 페더레이션 응용 프로그램을 수정할 수 있습니다. 위의 그림과 같이 탄력적인 확장 API를 통한 응용 프로그램 연결은 분할된 데이터베이스 맵 관리자를 통해 적절한 페더레이션 멤버(지금은 분할된 데이터베이스)에 라우팅됩니다. 페더레이션 멤버를 분할된 데이터베이스 맵 관리자를 매핑하면 두 버전의 응용 프로그램(페더레이션을 사용하는 응용 프로그램, 탄력적인 확장을 사용하는 응용 프로그램)이 나란히 실행되어 기능을 확인할 수 있습니다.   

응용 프로그램을 마이그레이션하는 동안 기존 응용 프로그램에 대해 두 가지 핵심 수정 사항을 수행해야 합니다.


#### 변경 1: 분할된 관리자 개체를 인스턴스화합니다. 

페더레이션과 달리 탄력적인 확장 API는 **ShardMapManager** 클래스를 통해 분할된 데이터베이스 맵 관리자를 조작합니다. **ShardMapManager** 개체 및 분할된 데이터베이스 맵의 인스턴스화는 다음과 같이 수행할 수 있습니다.
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 변경 2: 적절한 분할된 데이터베이스로 연결 라우팅

페더레이션을 통해 USE FEDERATION 명령을 사용한 특정 페더레이션 멤버에 대한 연결은 다음과 같습니다.  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

탄력적인 확장 API를 사용하면 **RangeShardMap** 클래스의 **OpenConnectionForKey** 메서드를 사용하는 [데이터 종속 라우팅](./sql-database-elastic-scale-data-dependent-routing.md)을 통해 특정 분할된 데이터베이스에 대한 연결이 설정됩니다. 

    //Connect and issue queries on the shard with key=100 
    using (SqlConnection conn = rangeShardMap.OpenConnectionForKey(100, csb))  
    { 
         using (SqlCommand cmd = new SqlCommand()) 
         { 
            cmd.Connection = conn; 
            cmd.CommandText = "SELECT * FROM customer";
     
            using (SqlDataReader dr = cmd.ExecuteReader()) 
            { 
                  //Perform action on dr 
            } 
        } 
    }

이 섹션의 단계는 필요하지만 발생하는 모든 마이그레이션 시나리오를 해결하지 못할 수 있습니다. 자세한 내용은 [탄력적인 확장](./sql-database-elastic-scale-introduction.md) 및 [API 참조의 개념적 개요](http://go.microsoft.com/?linkid=9862604)를 참조하세요.

## 기존 페더레이션 멤버 외부 전환 

![Switch out the federation members for the shards][3]

탄력적 확장 API를 포함하여 응용 프로그램이 수정되면 페더레이션 응용 프로그램의 마이그레이션에서 마지막 단계는 페더레이션 멤버를 **외부 전환**합니다. 자세한 내용은 [ALTER FEDERATION(Azure SQL 데이터베이스](http://msdn.microsoft.com/ko-kr/library/azure/hh597475.aspx)를 참조하세요. 특정 페더레이션 멤버에 대한 **SWITCH OUT** 실행의 최종 결과는 페더레이션 멤버를 일반 Azure SQL 데이터베이스(다른 Azure SQL 데이터베이스와 다르지 않음)로 렌더링하는 모든 페더레이션 제약 조건 및 메타데이터를 제거하는 것입니다. 

페더레이션 멤버에 대해 **SWITCH OUT**을 실행하는 작업은 단방향 작업이므로 실행 취소할 수 없습니다. 이 명령을 수행한 후에는 결과 데이터베이스를 페더레이션에 다시 추가할 수 없으며 USE FEDERATION 명령은 이 데이터베이스에 대해 더 이상 작동하지 않습니다. 

전환을 수행하려면 페더레이션 멤버를 외부 전환하는 추가 인수가 ALTER FEDERATION 명령에 추가되었습니다.  개별 페더레이션 멤버에 대해 명령을 실행할 수 있지만 페더레이션 마이그레이션 유틸리티에서 각 페더레이션 멤버를 프로그래밍 방식으로 반복하고 전환 작업을 수행하는 기능을 제공합니다. 

모든 기존 페더레이션 멤버에 대해 전환이 수행되면 응용 프로그램의 마이그레이션이 수행됩니다.  
페더레이션 마이그레이션 유틸리티는 다음 기능을 제공합니다. 

1.    분할된 데이터베이스 맵 관리자로의 페더레이션 루트 복제를 수행합니다.  새 Azure SQL 데이터베이스(권장) 또는 기존 페더레이션 루트 데이터베이스에서 기존의 분할된 데이터베이스 맵 관리자를 전환하도록 선택할 수 있습니다.
2.    페더레이션의 모든 페더레이션 멤버에 대해 SWITCH OUT을 실행합니다.


##기능 비교  
탄력적인 확장이 많은 추가 기능(예: [다중 분할 쿼리](./sql-database-elastic-scale-multishard-querying.md), [분할된 데이터베이스 분할 및 병합](./sql-database-elastic-scale-overview-split-and-merge.md), [분할된 데이터베이스 탄력성](./sql-database-elastic-scale-elasticity.md), [클라이언트 쪽 캐싱](./sql-database-elastic-scale-shard-map-management.md) 등)을 제공하지만 탄력적인 확장에서 지원되지 않는 몇 가지 주목할 만한 페더레이션 기능이 있습니다.
  

- **FILTERING=ON**을 사용합니다. 탄력적인 확장은 현재 행 수준 필터링을 지원하지 않습니다. 이 문제를 해결하는 한 가지 방법은 다음과 같이 분할된 데이터베이스에 대해 실행된 쿼리에 필터링 논리를 작성하는 것입니다. 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

다음과 결과를 생성합니다.

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- 탄력적인 확장 **분할** 기능은 완전하게 온라인 상태가 아닙니다. 분할 작업 중 이동하는 동안 개별 shardlet이 오프라인이 됩니다.
- 탄력적인 확장/분할 기능을 사용하려면 수동 데이터베이스 프로비전 및 스키마 관리가 필요합니다.

## 추가 고려 사항

* Web 및 Business Edition과 페더레이션이 2015년 가을부터는 사용되지 않습니다. 페더레이션 응용 프로그램의 마이그레이션 과정에서 Basic, Standard 및 Premium Edition에 대한 성능 테스트를 수행하는 것이 좋습니다. 

* 페더레이션 멤버에 대해 SWITCH OUT 문을 수행하면 결과적인 데이터베이스에서 Azure SQL 데이터베이스 기능(새로운 Edition, 백업 PITR 감사 등)을 모두 활용할 수 있습니다. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[페더레이션 루트에서 분할된 데이터베이스 맵 관리자 만들기]:#create-shard-map-manager
[기존 응용 프로그램 수정]:#Modify-the-Existing-Application
[기존 페더레이션 멤버 외부 전환]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png
