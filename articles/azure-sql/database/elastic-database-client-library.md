---
title: 확장성 있는 클라우드 데이터베이스 빌드
description: Elastic Database 클라이언트 라이브러리를 사용 하 여 확장 가능한 .NET 데이터베이스 앱을 빌드 하세요.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: bfe5dc00ba0255520c04ea85157f0b8bdc71b590
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84038514"
---
# <a name="building-scalable-cloud-databases"></a>확장성 있는 클라우드 데이터베이스 빌드
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database의 확장성 있는 도구 및 기능을 사용하여 데이터베이스 규모 확장을 쉽게 달성할 수 있습니다. 특히, **Elastic Database 클라이언트 라이브러리** 를 사용하여 규모 확장된 데이터베이스를 만들고 관리할 수 있습니다. 이 기능을 사용 하면 Azure SQL Database에서 수백 또는 수천 개의 데이터베이스를 사용 하 여 분할 된 응용 프로그램을 쉽게 개발할 수 있습니다.

다운로드하려면:

* 라이브러리의 Java 버전: [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools) 참조
* 라이브러리의 .NET 버전: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) 참조

## <a name="documentation"></a>설명서

1. [Elastic Database 도구 시작 하기](elastic-scale-get-started.md)
2. [Elastic Database 기능](elastic-scale-introduction.md)
3. [분할된 데이터베이스 맵 관리](elastic-scale-shard-map-management.md)
4. [확장하기 위해 기존 데이터베이스 마이그레이션](elastic-convert-to-use-elastic-tools.md)
5. [데이터 종속 라우팅](elastic-scale-data-dependent-routing.md)
6. [다중 분할된 쿼리](elastic-scale-multishard-querying.md)
7. [Elastic Database 도구를 사용하여 분할된 데이터베이스 추가하기](elastic-scale-add-a-shard.md)
8. [Elastic Database 도구 및 행 수준 보안을 사용 하는 다중 테 넌 트 응용 프로그램](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [클라이언트 라이브러리 앱 업그레이드](elastic-scale-upgrade-client-library.md) 
10. [탄력적 쿼리 개요](elastic-query-overview.md)
11. [Elastic Database 도구 용어집](elastic-scale-glossary.md)
12. [엔터티 프레임 작업과 함께 Elastic Database 클라이언트 라이브러리](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Dapper를 사용 하는 Elastic Database 클라이언트 라이브러리](elastic-scale-working-with-dapper.md)
14. [분할-병합 도구](elastic-scale-overview-split-and-merge.md)
15. [분할된 맵 관리자에 대한 성능 카운터](elastic-database-client-library.md) 
16. [Elastic Database tools에 대 한 FAQ](elastic-scale-faq.md)

## <a name="client-capabilities"></a>클라이언트 기능

*분할* 을 사용한 애플리케이션 규모 확장은 개발자뿐 아니라 관리자에게도 과제를 제공합니다. 클라이언트 라이브러리는 개발자와 관리자가 규모 확장된 데이터베이스를 관리할 수 있는 도구를 제공하여 관리 작업을 간소화합니다. 일반적인 예로 "분할된 데이터베이스"라는 관리할 수많은 데이터베이스가 있습니다. 고객은 동일한 데이터베이스에 함께 배치되며 고객당 하나의 데이터베이스가 있습니다(단일 테넌트 체계). 클라이언트 라이브러리에는 다음과 같은 기능이 포함됩니다.

- 분할 된 데이터베이스 **맵 관리**: "분할 된 데이터베이스 맵 관리자" 라는 특수 데이터베이스를 만듭니다. 분할된 데이터베이스 맵 관리는 분할된 데이터베이스에 대한 메타데이터를 관리하는 애플리케이션의 기능입니다. 개발자는 이 기능을 사용하여 데이터베이스를 분할된 데이터베이스처럼 등록하고, 개별 분할 키 또는 키 값 범위의 데이터베이스들 매핑을 설명하고, 숫자와 데이터베이스 조합이 용량 변경 내용을 반영하여 수정될 때 이 메타데이터를 유지 관리합니다. Elastic Database 클라이언트 라이브러리가 없으면 분할을 구현할 때 관리 코드를 작성 하는 데 많은 시간을 소비 해야 합니다. 자세한 내용은 [분할된 데이터베이스 맵 관리](elastic-scale-shard-map-management.md)를 참조하세요.

- **데이터 종속 라우팅**: 애플리케이션에 들어오는 요청이 있다고 가정하겠습니다. 요청의 분할 키 값에 따라 애플리케이션은 키 값을 기준으로 올바른 데이터베이스를 결정해야 합니다. 그런 다음 요청을 처리하는 데이터베이스에 대한 연결을 엽니다. 데이터 종속 라우팅은 애플리케이션의 분할된 데이터베이스 맵에 대한 간편한 단일 호출을 사용하여 연결을 열 수 있는 기능을 제공합니다. 데이터 종속 라우팅은 현재 Elastic Database 클라이언트 라이브러리의 기능에 포함 되는 인프라 코드의 또 다른 영역 이었습니다. 자세한 내용은 [데이터 종속 라우팅](elastic-scale-data-dependent-routing.md)을 참조하세요.
- **MSQ(다중 분할된 데이터베이스 쿼리)**: 분할된 다중 데이터베이스 쿼리 요청은 한 요청이 여러(또는 모든) 분할된 데이터베이스와 관련된 경우 작동합니다. 다중 분할된 데이터베이스 쿼리는 모든 분할된 데이터베이스 또는 분할된 데이터베이스 집합에서 동일한 T-SQL 코드를 실행합니다. 참여하는 분할된 데이터베이스의 결과는 UNION ALL 의미 체계를 사용하는 전반적인 결과 집합으로 병합됩니다. 이 기능은 클라이언트 라이브러리를 통해 표시되며 연결 관리, 스레드 관리, 오류 처리, 중간 결과 처리 등의 여러 작업을 처리합니다. MSQ는 수백 개까지 분할된 데이터베이스를 쿼리할 수 있습니다. 자세한 내용은 [다중 분할된 데이터베이스 쿼리](elastic-scale-multishard-querying.md)를 참조하세요.

일반적으로 Elastic Database 도구를 사용 하는 고객은 자체의 의미 체계를 갖는 분할 간 작업과 달리 분할 된 로컬 작업을 제출할 때 전체 T-sql 기능을 얻을 수 있습니다.



## <a name="next-steps"></a>다음 단계

- 클라이언트 라이브러리 ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.net](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/))를 Elastic Database 하 여 라이브러리를 **다운로드** 합니다.

- [Elastic Database 도구 시작](elastic-scale-get-started.md) -클라이언트 함수를 보여 주는 **샘플 앱** 을 사용해 보세요.

- GitHub([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - 코드 개선에 참여합니다.
- [Azure SQL Database 탄력적 쿼리](elastic-query-overview.md) - 탄력적 쿼리 사용.

- [확장 클라우드 데이터베이스 간  데이터 이동](elastic-scale-overview-split-and-merge.md) - **split-merge 도구** 사용 지침



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

