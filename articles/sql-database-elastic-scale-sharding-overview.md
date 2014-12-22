<properties title="Sharding Overview" pageTitle="분할 개요" description="Reasons for sharding: scale database resources to increase availability or performance." metaKeywords="sharding, scaling, elastic scale, Azure SQL Database" services="sql-database" documentationCenter="" manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />
#분할 개요 

##분할의 원칙 

**분할**이란 여러 독립 데이터베이스에 걸쳐 동일하게 구조화된 많은 데이터를 분산하는 기법입니다. 특히 최종 고객 또는 비즈니스에 대한 SAAS(Software as a Service) 제품으로 소프트웨어를 만드는 클라우드 개발자가 이 기법을 많이 사용합니다. 이러한 최종 고객을 보통 "테넌트"라고 합니다. 분할은 다음과 같은 여러 가지 이유로 필요할 수 있습니다. 

* 총 데이터 양이 너무 커서 단일 데이터베이스의 제약 조건에 맞지 않습니다. 
* 전반적인 작업의 트랜잭션 처리량이 단일 데이터베이스의 기능을 초과합니다. 
* 테넌트는 물리적으로 서로 격리되어야 할 수 있으므로 각 테넌트에는 별도의 데이터베이스가 필요합니다. 
* 데이터베이스의 다양한 섹션은 규정 준수, 성능 또는 지정학적인 이유로 여러 지역에 상주해야 할 수 있습니다. 
 
분할은 응용 프로그램의 모든 트랜잭션이 분할 키의 단일 값으로 제한될 수 있는 경우에 가장 적합합니다. 그렇게 하면 모든 트랜잭션이 특정 데이터베이스에 로컬이 됩니다. 

일부 응용 프로그램은 각 테넌트에 대해 별도의 데이터베이스를 만드는 가장 간단한 접근 방식을 사용합니다. 이것이 바로 테넌트 세분성 수준으로 격리, 백업/복원 기능 및 리소스 확장을 제공하는 **단일 테넌트 분할 패턴**입니다. 단일 테넌트 분할을 사용하면 각 데이터베이스가 특정 테넌트 ID 값(또는 고객의 키 값)과 연결되지만 해당 키가 데이터 자체에 존재하지 않아도 됩니다. 각 요청을 적절한 데이터베이스에 라우팅하는 것은 응용 프로그램의 책임입니다. 

![][1]

다른 시나리오에서는 여러 테넌트를 개별 데이터베이스로 격리하지 않고 함께 데이터베이스에 포함합니다. 이것이 일반적인 **다중 테넌트 분할 패턴**이며, 응용 프로그램이 다수의 매우 작은 테넌트를 관리한다는 사실과, 비용, 효율성을 고려한 패턴입니다. 다중 테넌트 분할에서 데이터베이스 테이블의 행은 모두 테넌트 ID 또는 키 분할을 식별하는 키 ID를 제공하도록 설계되었습니다. 다시 한 번 말하지만, 응용 프로그램 계층이 적절한 데이터베이스로 테넌트의 요청을 라우팅하는 작업을 담당합니다. 

분산된 장치에서 데이터 수집과 같은 다른 시나리오에서는 분할을 사용하여 일정 기간까지 분산된 데이터베이스 집합을 채울 수 있습니다. 예를 들어, 매일 또는 주별로 별도의 데이터베이스를 전용할 수 있습니다. 이 경우 분할 키는 날짜를 나타내는 정수(분할된 테이블의 모든 행에 있음)일 수 있으며, 응용 프로그램에서 문제의 범위를 포함하는 데이터베이스의 하위 집합으로 날짜 범위에 대한 정보를 검색하는 쿼리를 라우팅해야 합니다.

사용하는 분할 모델과 상관없이, **분할된 데이터베이스 맵**이라고 하는 특수한 데이터 구조가 분할 키 값과 데이터베이스를 연결하는 조회 테이블 역할을 합니다. 따라서 응용 프로그램에서 데이터베이스 요청에 대한 라우팅을 수행할 수 있습니다. **데이터 종속 라우팅**이라고 하는 이 기능은 응용 프로그램에서 분할된 데이터 계층을 사용하는 데 필요한 핵심 기능입니다. [탄력적인 확장 클라이언트 API](http://go.microsoft.com/?linkid=9862592)는 [분할된 데이터베이스 맵을 관리하고](http://go.microsoft.com/?linkid=9862595) 응용 프로그램에서 간편하고 효율적인 사용[Data-depenendent routing capabilities](http://go.microsoft.com/?linkid=9862596)을 가능하게 하는 데 필요한 여러 기능 집합을 제공합니다. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-sharding-overview/tenancy.png
