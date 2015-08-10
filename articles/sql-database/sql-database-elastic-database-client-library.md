<properties
    pageTitle="Azure SQL 데이터베이스 - 클라이언트 라이브러리"
    description="확장 가능한 .NET 데이터베이스 응용 프로그램 구축"
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="sidneyh"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2015"
    ms.author="sidneyh"/>

# 탄력적 데이터베이스 클라이언트 라이브러리 개요

**탄력적 데이터베이스 클라이언트 라이브러리**를 통해 Microsoft Azure에서 호스팅되는 수백 또는 수천 개의 Azure SQL 데이터베이스 컬렉션을 관리하는 클라이언트 응용 프로그램을 빌드할 수 있습니다. 이러한 컬렉션은 각 테넌트가 데이터베이스로 프로비저닝되는 일반적으로 단일 테넌트 아키텍처인 SaaS(Software as a Service) 응용 프로그램에 대해 일반적으로 사용됩니다. 이러한 응용 프로그램을 빌드하고 관리하는 것이 라이브러리의 목표입니다.

클라이언트 라이브러리는 Visual Studio 및 Nuget을 사용하여 모든 응용 프로그램 프로젝트에 설치할 수 있는 .NET Framework 라이브러리입니다. 클라이언트 라이브러리는 [탄력적 데이터베이스 도구](sql-azure-database-elastic-scale-introduction.md) 집합의 일부입니다.


## 클라이언트 기능

*분할*(아래에서 논의된)을 사용한 확장 응용 프로그램 개발, 확장 및 관리는 개발자뿐 아니라 관리자에게도 도전 과제를 제공합니다. 클라이언트 라이브러리는 이 두 개의 기능으로 삶을 더욱 쉽게 만들어 줍니다. 개요 아래의 숫자는 탄력적 데이터베이스 클라이언트 라이브러리와 분할-병합 도구를 사용해 주요기능을 배달합니다. 그림은 많은 데이터베이스 환경, 그리고 각각의 데이터베이스는 분할된 데이터베이스를 해당하는 것을 보여줍니다. 도구는 다음과 같은 특정 기능을 통해 보다 쉽게 분할 된 데이터베이스 Azure SQL DB 응용 프로그램 개발을 확인합니다.

여기에 사용되는 용어의 정의는 [탄력적 데이터베이스 도구 용어집](sql-database-elastic-scale-glossary.md)을 참조하세요.

![탄력적인 확장 기능][1]

1.  **분할된 데이터베이스 맵 관리**: 분할된 데이터베이스 컬렉션을 관리하려면 "분할된 데이터베이스 맵 관리자"라는 특별한 데이터베이스가 만들어집니다. 분할된 데이터베이스 맵 관리는 분할된 데이터베이스에 대한 다양한 메타데이터를 관리하는 응용 프로그램의 기능입니다. 개발자는 이 기능을 사용하여 데이터베이스를 분할된 데이터베이스처럼 등록하고, 개별 분할 키 또는 키 값 범위의 데이터베이스들 매핑을 설명하고, 숫자와 데이터베이스 조합이 용량 변경 내용을 반영하여 수정될 때 이 메타데이터를 유지 관리합니다. 탄력적 데이터베이스 클라이언트 라이브러리가 없으면 분할을 구현하는 경우 관리 코드를 작성하는데 많은 시간을 투자해야 합니다. 자세한 내용은 [분할된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md)를 참조하세요.

* **데이터 종속 라우팅**: 응용 프로그램에 들어오는 요청이 있다고 가정하겠습니다. 요청의 분할 키 값에 따라 응용 프로그램에서 이 키 값에 대한 데이터가 저장된 올바른 데이터베이스를 확인한 다음, 이것에 대한 연결을 열어서 요청을 처리해야 합니다. 데이터 종속 라우팅은 응용 프로그램의 분할된 데이터베이스 맵에 대한 간편한 단일 호출을 사용하여 연결을 열 수 있는 기능을 제공합니다. 데이터 종속 라우팅은 지금은 탄력적 데이터베이스 클라이언트 라이브러리의 기능에서 담당하는 인프라 코드의 다른 영역이었습니다. 자세한 내용은 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)을 참조하세요.

* **MSQ(다중 분할된 데이터베이스 쿼리)**: 분할된 다중 데이터베이스 쿼리 요청은 한 요청이 여러(또는 모든) 분할된 데이터베이스와 관련된 경우 작동합니다. 다중 분할된 데이터베이스 쿼리는 모든 분할된 데이터베이스 또는 분할된 데이터베이스 집합에서 동일한 T-SQL 코드를 실행합니다. 참여하는 분할된 데이터베이스의 결과는 UNION ALL 의미 체계를 사용하는 전반적인 결과 집합으로 병합됩니다. 이 기능은 클라이언트 라이브러리를 통해 표시되며 연결 관리, 스레드 관리, 오류 처리, 중간 결과 처리 등의 여러 작업을 처리합니다. MSQ는 수백 개까지 분할된 데이터베이스를 쿼리할 수 있습니다. 자세한 내용은 [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md)를 참조하세요.

일반적으로, 고객의 탄력적 데이터베이스 도구 사용은 고유한 의미 체계를 가진 분할된 데이터베이스 간 작업과 반대로 분할된 데이터베이스 로컬 작업을 제출할 때 전체 T-SQL 기능을 가져올 수 있습니다.

## 다음 단계

클라이언트 함수를 보여 주는 [샘플 응용 프로그램](sql-database-elastic-scale-get-started.md)을 시도하세요.

라이브러리를 설치하려면 [탄력적 데이터베이스 클라이언트 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)로 이동하세요.

분할 병합 도구 사용에 대한 지침은 [분할 병합 도구 개요](sql-database-elastic-scale-overview-split-and-merge.md)를 참조하세요.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=July15_HO5-->