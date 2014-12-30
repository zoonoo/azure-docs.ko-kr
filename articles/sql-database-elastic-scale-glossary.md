<properties title="Azure Elastic Scale Glossary" pageTitle="Azure 탄력적인 확장 용어집" description="Azure SQL 데이터베이스의 탄력적인 확장 기능에 사용되는 용어에 대한 설명" metaKeywords="sharding,elastic scale, Azure SQL DB sharding" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#탄력적인 확장 용어집
Azure SQL 데이터베이스의 탄력적인 확장 기능에 대해 정의된 용어는 다음과 같습니다.

![Elastic Scale terms][1]

**데이터베이스**: Azure SQL 데이터베이스입니다. 

**데이터 종속 라우팅**: 응용 프로그램이 지정된 특정 분할 키의 분할에 연결할 수 있는 기능입니다. **다중 분할 쿼리**와 비교됩니다.

**전역 분할 맵**: 분할 키와 **분할 집합** 내의 해당 분할 사이의 매핑 집합입니다. GSM은 **분할 맵 관리자**에 저장됩니다. **로컬 분할 맵**과 비교됩니다.

**목록 분할 맵**: 분할 키가 개별적으로 매핑되는 분할 맵입니다. **범위 분할 맵**과 비교됩니다.   

**로컬 분할 맵**: 분할에 저장된 로컬 분할 맵에는 분할에 있는 shardlet에 대한 매핑이 포함됩니다.


**다중 분할 쿼리**: 여러 분할에 대해 쿼리를 실행할 수 있는 기능입니다. 결과 집합이 UNION ALL 의미 체계를 사용하여 반환됩니다("팬아웃 쿼리"라고도 함). **데이터 종속 라우팅**과 비교됩니다.

**범위 공유 맵**: 공유 분산 전략이 여러 연속 값 범위를 기반으로 하는 분할 맵입니다. 


**참조 테이블**: 분할되지 않지만 분할 간에 복제되는 테이블입니다. 

**분할**: 분할 데이터 집합의 데이터를 저장하는 Azure SQL 데이터베이스입니다. 

**분할 탄력성**(SE): **수평 확장**과 **수직 확장**을 모두 수행할 수 있는 기능입니다.

**분할 테이블**: 분할되는 테이블 즉, 데이터가 분할 키 값을 기준으로 분할 간에 분산되는 테이블입니다. 

**분할 키**: 데이터가 분할 간에 분산되는 방법을 결정하는 열 값입니다. 값 형식은 int, bigint, varbinary 또는 uniqueidentifier 중 하나일 수 있습니다. 

**분할 집합**: 분할 맵 관리자에서 동일한 분할 맵에 할당된 분할의 컬렉션입니다.  

**Shardlet**: 분할의 단일 분할 키 값과 연결된 모든 데이터입니다. Shardlet은 분할 테이블을 재분산할 때 가능한 가장 작은 데이터 이동 단위입니다. 

**분할 맵**: 분할 키와 해당 분할 간의 매핑 집합입니다.

**분할 맵 관리자**: 분할 맵, 분할 위치 및 하나 이상의 분할 집합에 대한 매핑을 포함하는 관리 개체 및 데이터 저장소입니다.

![Mappings][2]


##동사

**수평 확장**: 분할 맵에 분할을 추가하거나 제거하여 분할 컬렉션을 규모 확장(또는 규모 감축)하는 작업입니다.

**병합**: 두 분할의 shardlet을 하나의 분할로 이동하고 이에 따라 분할 맵을 업데이트하는 작업입니다.

**Shardlet 이동**: 단일 shardlet을 다른 분할로 이동하는 작업입니다. 

**분할**: 구조가 동일한 데이터를 분할 키를 기준으로 여러 데이터베이스 간에 수평적으로 분할하는 작업입니다.

**분할**: 한 분할에서 다른(일반적으로 새) 분할로 여러 shardlet을 이동하는 작업입니다. 분할 키는 사용자가 분할 지점으로 지정합니다.

**수직 확장**: 개별 분할의 성능 수준을 강화(또는 규모 축소)하는 작업입니다. 예: 분할을 Standard에서 Premium으로 변경(성능상의 이유로 필요) 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]  

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png



<!--HONumber=35_1-->
