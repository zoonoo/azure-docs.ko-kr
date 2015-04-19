<properties 
	pageTitle="페더레이션 마이그레이션" 
	description="페더레이션 기능을 사용하여 빌드된 기존 앱을 탄력적인 확장 모델로 마이그레이션하는 단계를 간략히 설명합니다." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="Joseidz@microsoft.com"/>

# 페더레이션 마이그레이션 

Azure SQL 데이터베이스 페더레이션 기능은 2015년 9월부터 Web/Business Edition과 함께 더 이상 사용되지 않습니다. 이때부터 페더레이션 기능을 사용하는 응용 프로그램은 더 이상 실행되지 않습니다. 마이그레이션에 성공하기 위해 충분히 계획하고 실행할 수 있도록 마이그레이션 작업을 최대한 빨리 시작하는 것이 좋습니다. 이 문서는 현재 페더레이션 응용 프로그램을 [Azure SQL DB 탄력적인 확장 미리 보기 API](http://go.microsoft.com/?linkid=9862592)로 원활하게 마이그레이션하는 방법을 보여 주는 컨텍스트, 예제 및 페더레이션 마이그레이션 유틸리티에 대한 소개를 제공합니다. 이 문서의 목표는 데이터 이동 없이 페더레이션 응용 프로그램을 마이그레이션하기 위한 추천 단계를 안내하는 것입니다.

기존 페더레이션 응용 프로그램을 탄력적인 확장 API를 사용하는 응용 프로그램으로 마이그레이션하는 주요 단계는 다음 세 가지입니다.

1. [페더레이션 루트에서 분할된 데이터베이스 맵 관리자 만들기] 
2. [기존 응용 프로그램 수정]
3. [기존 페더레이션 멤버 외부 전환]
    

### 마이그레이션 샘플 도구
이 프로세스를 지원하기 위해 [페더레이션 마이그레이션 유틸리티](http://go.microsoft.com/?linkid=9862613)가 작성되었습니다. 이 유틸리티에서 1~3단계를 수행합니다. 

## <a name="create-shard-map-manager"></a>페더레이션 루트에서 분할된 데이터베이스 맵 관리자 만들기
페더레이션 응용 프로그램을 마이그레이션하는 첫 번째 단계에서는 페더레이션 루트의 메타데이터를 분할 맵 관리자의 구문으로 복제합니다. 

![Clone the federation root to the shard map manager][1]
 
테스트 환경에서 기존 페더레이션 응용 프로그램으로 시작합니다.
 
탄력적인 확장 [분할된 데이터베이스 맵 관리자](http://go.microsoft.com/?linkid=9862595)의 구문에 페더레이션 루트 메타데이터를 복제하려면 **페더레이션 마이그레이션 유틸리티**를 사용합니다. 페더레이션 루트와 유사한 분할된 데이터베이스 맵 관리자 데이터베이스가 분할된 데이터베이스 맵(즉, 페더레이션), 분할된 데이터베이스(즉, 페더레이션 멤버) 및 각 범위 매핑에 대한 참조를 포함하는 독립 실행형 데이터베이스입니다. 

분할된 데이터베이스 맵 관리자에 페더레이션 루트를 복제하면 메타데이터가 복사 및 변환됩니다. 페더레이션 루트의 메타데이터는 변경되지 않습니다. 페더레이션 마이그레이션 유틸리티와 페더레이션 루트를 복제하는 것은 지정 시점 작업이며 분할된 데이터베이스 맵 또는 페더레이션 루트에 대한 변경 내용은 다른 각 데이터 저장소에 반영되지 않습니다. 새로운 API를 테스트하는 동안 페더레이션 루트를 변경하는 경우 페더레이션 마이그레이션 유틸리티를 사용하여 현재 상태를 나타내는 분할된 데이터베이스 맵을 새로 고칠 수 있습니다. 

![Migrate the existing app to use the Elastic Scale APIs][2]

## 기존 응용 프로그램 수정 

적절한 분할된 데이터베이스 맵 관리자와 분할된 데이터베이스 맵 관리자에 등록된 페더레이션 멤버 및 범위(마이그레이션 유틸리티를 통해)을 사용하여, 탄력적인 확장 API를 활용하도록 기존 페더레이션 응용 프로그램을 수정할 수 있습니다. 위의 그림과 같이 탄력적인 확장 API를 통한 응용 프로그램 연결은 분할된 데이터베이스 맵 관리자를 통해 적절한 페더레이션 멤버(지금은 분할된 데이터베이스)에 라우팅됩니다. 페더레이션 멤버를 분할된 데이터베이스 맵 관리자를 매핑하면 두 버전의 응용 프로그램(페더레이션을 사용하는 응용 프로그램, 탄력적인 확장을 사용하는 응용 프로그램)이 나란히 실행되어 기능을 확인할 수 있습니다.   

응용 프로그램을 마이그레이션하는 동안 기존 응용 프로그램에 대해 두 가지 핵심 수정 사항을 수행해야 합니다.


#### 변경 1: 분할 맵 관리자 개체 인스턴스화 

페더레이션과 달리 탄력적인 확장 API는 **ShardMapManager** 클래스를 통해 분할된 데이터베이스 맵 관리자를 조작합니다. **ShardMapManager** 개체 및 분할된 데이터베이스 맵의 인스턴스화는 다음과 같이 수행할 수 있습니다.
     
    //Instantiate ShardMapManger Object 
    ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
                            connectionStringSMM, ShardMapManagerLoadPolicy.Lazy); 
    RangeShardMap<T> rangeShardMap = shardMapManager.GetRangeShardMap<T>(shardMapName) 
    
#### 변경 2: 연결을 적절한 분할로 라우팅 

페더레이션에서는 다음과 같이 USE FEDERATION 명령을 사용하여 특정 페더레이션 구성원에 대한 연결을 설정합니다.  

    USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF`

탄력적인 확장 API에서는 특정 분할된 데이터베이스에 대한 연결이 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)을 통해 설정됩니다 이때 **RangeShardMap** 클래스의 **OpenConnectionForKey** 메서드를 사용합니다. 

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

이 섹션의 단계는 필요하지만 발생하는 마이그레이션 시나리오를 모두 해결할 수는 없습니다. 자세한 내용은 [탄력적인 확장에 대한 개념 개요](sql-database-elastic-scale-introduction.md) 및 [API 참조](http://go.microsoft.com/?linkid=9862604)를 참조하세요.

## 기존 페더레이션 멤버 외부 전환 

![Switch out the federation members for the shards][3]

응용 프로그램이 탄력적인 확장 API를 포함하여 수정되면, 페더레이션 응용 프로그램 마이그레이션의 마지막 단계는 페더레이션 구성원을 **SWITCH OUT**하는 것입니다. 자세한 내용은 [ALTER FEDERATION(Azure SQL 데이터베이스](http://msdn.microsoft.com/library/dn269988(v=sql.120).aspx)에 대한 MSDN 참조를 참조하세요. 특정 페더레이션 구성원에 대한 **SWITCH OUT** 실행의 최종 결과로 페더레이션 제약 조건이 모두 제거되고 페더레이션 멤버가 다른 Azure SQL 데이터베이스와 다르지 않은 일반 Azure SQL 데이터베이스로 메타데이터 렌더링됩니다.  

페더레이션 멤버에 대해 **SWITCH OUT**을 실행하는 작업은 단방향 작업이므로 실행 취소할 수 없습니다. 이 명령을 수행한 후에는 결과 데이터베이스를 페더레이션에 다시 추가할 수 없으며 USE FEDERATION 명령은 이 데이터베이스에 대해 더 이상 작동하지 않습니다. 

전환을 수행하려면 페더레이션 구성원을 SWITCH OUT하기 위한 인수를 ALTER FEDERATION 명령에 추가해야 합니다.  개별 페더레이션 멤버에 대해 명령을 실행할 수 있지만 페더레이션 마이그레이션 유틸리티에서 각 페더레이션 멤버를 프로그래밍 방식으로 반복하고 전환 작업을 수행하는 기능을 제공합니다. 

모든 기존 페더레이션 멤버에 대해 전환이 수행되면 응용 프로그램의 마이그레이션이 수행됩니다.  
페더레이션 마이그레이션 유틸리티는 다음 기능을 제공합니다. 

1.    페더레이션 루트를 분할 맵 관리자로 복제.  기존 분할 맵 관리자를 새 Azure SQL 데이터베이스(권장)나 기존 페더레이션 루트 데이터베이스에 배치할 수 있습니다.
2.    페더레이션의 모든 페더레이션 멤버에 대해 SWITCH OUT을 실행합니다.


## 기능 비교  
탄력적인 확장에서는 여러 추가 기능(예: [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md), [분할된 데이터베이스 병합 및 분할](sql-database-elastic-scale-overview-split-and-merge.md), [분할된 데이터베이스 탄력성](sql-database-elastic-scale-elasticity.md), [클라이언트 쪽 캐싱](sql-database-elastic-scale-shard-map-management.md))을 제공합니다.등)을 제공하지만, 다음과 같은 몇 가지 중요한 페더레이션 기능이 탄력적인 확장에서 지원되지 않습니다.
  

- **FILTERING=ON** 사용. 탄력적인 확장은 현재 행 수준 필터링을 지원하지 않습니다. 이 문제를 해결하는 한 가지 방법은 다음과 같이 분할된 데이터베이스에 대해 실행된 쿼리에 필터링 논리를 작성하는 것입니다. 

        --Example of USE FEDERATION with FILTERING=ON
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=ON 
        SELECT * FROM customer

이 쿼리는 다음과 결과가 같습니다.

        --Example of USE FEDERATION with filtering in the WHERE clause 
        USE FEDERATION CustomerFederation(cid=100) WITH RESET, FILTERING=OFF 
        SELECT * FROM customer WHERE CustomerId = 100 

- 탄력적인 확장 **분할** 기능은 완전하게 온라인 상태가 아닙니다. 분할 작업 중 shardlet은 이동하는 동안 오프라인 상태로 전환됩니다.
- 탄력적인 확장 분할 기능을 사용하려면 수동 데이터베이스 프로비저닝과 스키마 관리가 필요합니다.

## 추가 고려 사항

* Web 및 Business Edition 모두 2015년 가을부터 페더레이션이 더 이상 사용되지 않습니다.  페더레이션 응용 프로그램을 마이그레이션할 때 Basic, Standard 및 Premium Edition에서 성능 테스트를 수행하는 것이 좋습니다. 

* 페더레이션 구성원에 대해 SWITCH OUT 문을 수행하면 결과 데이터베이스에서 Azure SQL 데이터베이스 기능(즉, 새 버전, 백업, PITR, 감사 등)을 모두 사용할 수 있습니다. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Anchors-->
[페더레이션 루트에서 분할된 데이터베이스 맵 관리자 만들기]:#create-shard-map-manager
[기존 응용 프로그램 수정]:#Modify-the-Existing-Application
[기존 페더레이션 멤버 외부 전환]:#Switch-Out-Existing-Federation-members


<!--Image references-->
[1]: ./media/sql-database-elastic-scale-federation-migration/migrate-1.png
[2]: ./media/sql-database-elastic-scale-federation-migration/migrate-2.png
[3]: ./media/sql-database-elastic-scale-federation-migration/migrate-3.png

<!--HONumber=47-->
