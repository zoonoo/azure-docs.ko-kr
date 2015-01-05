<properties title="Federations Migration" pageTitle="페더레이션 마이그레이션" description="페더레이션 기능을 사용하여 빌드된 기존 앱을 탄력적인 확장 모델로 마이그레이션하는 단계를 간략히 설명합니다." metaKeywords="sharding scaling, federations, Azure SQL DB sharding, Elastic Scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/30/2014" ms.author="sidneyh" />

#페더레이션 마이그레이션 

Azure SQL 데이터베이스 페더레이션 기능은 2015년 9월부터 Web/Business Edition과 함께 더 이상 사용되지 않습니다. 이때부터 페더레이션 기능을 사용하는 응용 프로그램은 더 이상 실행되지 않습니다. 마이그레이션에 성공하기 위해 충분히 계획하고 실행할 수 있도록 마이그레이션 작업을 최대한 빨리 시작하는 것이 좋습니다. 이 문서에서는 컨텍스트와 예제를 제공하고 현재 페더레이션 응용 프로그램을 [Azure SQL DB 탄력적인 확장 미리 보기 API](영문)으로 원활하게 마이그레이션하는 방법을 보여 주는 페더레이션 마이그레이션 유틸리티를(http://go.microsoft.com/?linkid=9862592)소개합니다. 이 문서의 목표는 데이터 이동 없이 페더레이션 응용 프로그램을 마이그레이션하기 위한 추천 단계를 안내하는 것입니다.

기존 페더레이션 응용 프로그램을 탄력적인 확장 API를 사용하는 응용 프로그램으로 마이그레이션하는 주요 단계는 다음 세 가지입니다.

1. [페더레이션 루트에서 분할 맵 관리자 만들기] 
2. [기존 응용 프로그램 수정]
3. [기존 페더레이션 구성원 요소 Switch Out]
    

### 마이그레이션 샘플 도구
이 프로세스를 지원하기 위해 [페더레이션 마이그레이션 유틸리티](영문)를(http://go.microsoft.com/?linkid=9862613) 만들었습니다. 이 유틸리티에서 1~3단계를 수행합니다. 

## <a name="create-shard-map-manager"></a>페더레이션 루트에서 분할 맵 관리자 만들기
페더레이션 응용 프로그램을 마이그레이션하는 첫 번째 단계에서는 페더레이션 루트의 메타데이터를 분할 맵 관리자의 구문으로 복제합니다. 

![Clone the federation root to the shard map manager][1]
 
테스트 환경에서 기존 페더레이션 응용 프로그램으로 시작합니다.
 
**페더레이션 마이그레이션 유틸리티**를 사용하여 페더레이션 루트 메타데이터를 탄력적인 확장 [분할 맵 관리자](영문)의 구문으로(http://go.microsoft.com/?linkid=9862595)복제합니다. 페더레이션 루트와 유사하게 분할 맵 관리자 데이터베이스는 분할 맵(즉, 페더레이션), 분할에 대한 참조(즉, 페더레이션 구성원) 및 해당 범위 매핑을 포함하는 독립 실행형 데이터베이스입니다. 

페더레이션 루트를 분할 맵 관리자로 복제하는 경우 메타데이터가 복사 및 변환됩니다. 페더레이션 루트의 메타데이터는 변경되지 않습니다. 페더레이션 마이그레이션 유틸리티를 사용한 페더레이션 루트의 복제는 지정 시간 작업이며 페더레이션 루트 또는 분할 맵에 대한 변경 내용이 각각의 다른 데이터 저장소에 반영되지 않습니다. 새 API 테스트 중 페더레이션 루트를 변경한 경우 페더레이션 마이그레이션 유틸리티로 분할 맵을 새로 고쳐 현재 상태를 나타낼 수 있습니다. 

![Migrate the existing app to use the Elastic Scale APIs][2]

## 기존 응용 프로그램 수정 

분할 맵 관리자를 사용할 수 있게 되고 마이그레이션 유틸리티를 통해 페더레이션 구성원 및 범위를 분할 맵 관리자에 등록한 경우 탄력적인 확장 API를 활용하도록 기존 페더레이션 응용 프로그램을 수정할 수 있습니다. 위 그림에 나온 대로 탄력적인 확장 API를 통한 응용 프로그램 연결은 분할 맵 관리자를 통해 적절한 페더레이션 구성원(이제 마찬가지로 분할)에 라우팅됩니다. 페더레이션 구성원을 분할 맵 관리자에 매핑하면 두 가지 버전의 응용 프로그램(하나는 페더레이션을 사용하고, 다른 하나는 탄력적인 확장 사용)을 나란히 실행하여 기능을 확인할 수 있습니다.   

응용 프로그램 마이그레이션 동안 기존 응용 프로그램의 다음 두 가지 핵심 사항을 수정해야 합니다.


#### 변경 1: 분할 맵 관리자 개체 인스턴스화 

페더레이션과 달리 탄력적인 확장 API는 **ShardMapManager** 클래스를 통해 분할 맵 관리자를 조작합니다. **ShardMapManager** 개체와 분할 맵은 다음과 같이 인스턴스화할 수 있습니다.
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 변경 2: 연결을 적절한 분할로 라우팅 

페더레이션에서는 다음과 같이 USE FEDERATION 명령을 사용하여 특정 페더레이션 구성원에 대한 연결을 설정합니다.  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

탄력적인 확장 API에서는 특정 분할에 대한 연결이 [데이터 종속 라우팅](영문)을 통해,(./sql-database-elastic-scale-data-dependent-routing.md) **RangeShardMap** 클래스의 **OpenConnectionForKey** 메서드를 사용하여 설정됩니다. 

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

이 섹션의 단계는 필요하지만 발생하는 마이그레이션 시나리오를 모두 해결할 수는 없습니다. 자세한 내용은 [탄력적인 확장에 대한 개념 개요](영문)(./sql-database-elastic-scale-introduction.md) 및 [API 참조](영문)를(http://go.microsoft.com/?linkid=9862604)참조하세요.

## 기존 페더레이션 구성원 요소 Switch Out 

![Switch out the federation members for the shards][3]

응용 프로그램이 탄력적인 확장 API를 포함하여 수정되면, 페더레이션 응용 프로그램 마이그레이션의 마지막 단계는 페더레이션 구성원을 **SWITCH OUT**하는 것입니다(자세한 내용은 [ALTER FEDERATION(Azure SQL Database](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx)에 대한 MSDN 참조). 특정 페더레이션 구성원에 대한 **SWITCH OUT** 실행의 최종 결과로 페더레이션 제약 조건이 모두 제거되고 페더레이션 구성원이 다른 Azure SQL 데이터베이스와 다르지 않은 일반 Azure SQL 데이터베이스로 메타데이터 렌더링됩니다.  

페더레이션 구성원에 대한 **SWITCH OUT**은 단방향 작업이며 실행 취소할 수 없습니다. 수행하면 결과로 얻은 데이터베이스는 페더레이션에 다시 추가할 수 없고 이 데이터베이스에 대해 USE FEDERATION 명령이 더 이상 작동하지 않습니다. 

전환을 수행하려면 페더레이션 구성원을 SWITCH OUT하기 위한 인수를 ALTER FEDERATION 명령에 추가해야 합니다.  개별 페더레이션 구성원에 대해 명령을 실행할 수는 없지만, 페더레이션 마이그레이션 유틸리티를 사용하여 프로그래밍 방식으로 각 페더레이션 구성원을 반복하여 전환 작업을 수행할 수 있습니다. 

기존 페더레이션 구성원 모두에 대해 전환이 수행되면 응용 프로그램 마이그레이션이 완료됩니다.  
페더레이션 마이그레이션 유틸리티를 사용하여 다음을 할 수 있습니다. 

1.    페더레이션 루트를 분할 맵 관리자로 복제.  기존 분할 맵 관리자를 새 Azure SQL 데이터베이스(권장)나 기존 페더레이션 루트 데이터베이스에 배치할 수 있습니다.
2.    페더레이션의 모든 페더레이션 구성원에 대해 SWITCH OUT 실행


##기능 비교  
탄력적인 확장에서는 여러 추가 기능(예: [다중 분할 쿼리](영문)(./sql-database-elastic-scale-multishard-querying.md), [분할 나누기 및 병합](영문)(./sql-database-elastic-scale-overview-split-and-merge.md), [분할 탄력성](영문)(./sql-database-elastic-scale-elasticity.md), [클라이언트 쪽 캐싱](./sql-database-elastic-scale-shard-map-management.md)등)을 제공하지만, 다음과 같은 몇 가지 중요한 페더레이션 기능이 탄력적인 확장에서 지원되지 않습니다.
  

- **FILTERING=ON** 사용. 탄력적인 확장에서는 현재 행 수준 필터링을 지원하지 않습니다. 예를 들어 마이그레이션에서 다음과 같이 분할에 대해 실행되는 쿼리에 필터링 논리를 포함합니다. 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

이 쿼리는 다음과 결과가 같습니다.

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- 탄력적인 확장 **분할** 기능은 완전히 온라인 상태가 아닙니다. 분할 작업 중 shardlet은 이동하는 동안 오프라인 상태로 전환됩니다.
- 탄력적인 확장 분할 기능을 사용하려면 수동 데이터베이스 프로비저닝과 스키마 관리가 필요합니다.

## 추가 고려 사항

* Web 및 Business Edition 모두 2015년 가을부터 페더레이션이 더 이상 사용되지 않습니다.  페더레이션 응용 프로그램을 마이그레이션할 때 Basic, Standard 및 Premium Edition에서 성능 테스트를 수행하는 것이 좋습니다. 

* 페더레이션 구성원에 대해 SWITCH OUT 문을 수행하면 결과 데이터베이스에서 Azure SQL 데이터베이스 기능(즉, 새 버전, 백업, PITR, 감사 등)을 모두 사용할 수 있습니다. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[페더레이션 루트에서 분할 맵 관리자 만들기]:#create-shard-map-manager
[기존 응용 프로그램 수정]:#Modify-the-Existing-Application
[기존 페더레이션 구성원 요소 Switch Out]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png

<!--HONumber=35.1-->
