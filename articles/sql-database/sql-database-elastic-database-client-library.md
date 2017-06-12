---
title: "확장성 있는 클라우드 데이터베이스 빌드 | Microsoft Docs"
description: "탄력적 데이터베이스 클라이언트 라이브러리와 함께 확장성 있는 .NET 데이터베이스 앱 빌드"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 1f11c52d-13c1-4994-b9b1-5b1ae2f9255f
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: ddove
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 509a77a5059a39f964a1d89099998a07fd2cbfd5
ms.contentlocale: ko-kr
ms.lasthandoff: 12/08/2016


---
# <a name="building-scalable-cloud-databases"></a>확장성 있는 클라우드 데이터베이스 빌드
Azure SQL 데이터베이스의 확장성 있는 도구 및 기능을 사용하여 데이터베이스 규모 확장을 쉽게 달성할 수 있습니다. 특히, **탄력적 데이터베이스 클라이언트 라이브러리** 를 사용하여 규모 확장된 데이터베이스를 만들고 관리할 수 있습니다. 이 기능을 통해 수백 또는 수천 개의 Azure SQL 데이터베이스를 사용하여 분할된 응용 프로그램을 쉽게 개발할 수 있습니다. [탄력적 작업](sql-database-elastic-jobs-powershell.md) 을 사용할 수 있습니다.

라이브러리를 설치하려면 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)로 이동합니다. 

## <a name="documentation"></a>설명서
1. [탄력적 데이터베이스 도구 시작하기](sql-database-elastic-scale-get-started.md)
2. [탄력적 데이터베이스 기능](sql-database-elastic-scale-introduction.md)
3. [분할된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md)
4. [확장하기 위해 기존 데이터베이스 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)
6. [다중 분할된 쿼리](sql-database-elastic-scale-multishard-querying.md)
7. [탄력적 데이터베이스 도구를 사용하여 분할된 데이터베이스 추가하기](sql-database-elastic-scale-add-a-shard.md)
8. [탄력적 데이터베이스 도구 및 행 수준 보안을 제공하는 다중 테넌트 응용 프로그램](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [클라이언트 라이브러리 앱 업그레이드](sql-database-elastic-scale-upgrade-client-library.md) 
10. [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)
11. [탄력적 데이터베이스 도구 용어집](sql-database-elastic-scale-glossary.md)
12. [엔터티 프레임 작업과 함께 탄력적 데이터베이스 클라이언트 라이브러리](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [탄력적 데이터베이스 클라이언트 라이브러리 및 Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [분할-병합 도구](sql-database-elastic-scale-overview-split-and-merge.md)
15. [분할된 맵 관리자에 대한 성능 카운터](sql-database-elastic-database-client-library.md) 
16. [탄력적 데이터베이스 도구 FAQ](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>클라이언트 기능
*분할* 을 사용한 응용 프로그램 규모 확장은 개발자뿐 아니라 관리자에게도 과제를 제공합니다. 클라이언트 라이브러리는 개발자와 관리자가 규모 확장된 데이터베이스를 관리할 수 있는 도구를 제공하여 관리 작업을 간소화합니다. 일반적인 예로 "분할된 데이터베이스"라는 관리할 수많은 데이터베이스가 있습니다. 고객은 동일한 데이터베이스에 함께 배치되며 고객당 하나의 데이터베이스가 있습니다(단일 테넌트 체계). 클라이언트 라이브러리에는 다음과 같은 기능이 포함됩니다.

- **분할된 데이터베이스 맵 관리**: "분할된 데이터베이스 맵 관리자"라는 특수 데이터베이스가 만들어집니다. 분할된 데이터베이스 맵 관리는 분할된 데이터베이스에 대한 메타데이터를 관리하는 응용 프로그램의 기능입니다. 개발자는 이 기능을 사용하여 데이터베이스를 분할된 데이터베이스처럼 등록하고, 개별 분할 키 또는 키 값 범위의 데이터베이스들 매핑을 설명하고, 숫자와 데이터베이스 조합이 용량 변경 내용을 반영하여 수정될 때 이 메타데이터를 유지 관리합니다. 탄력적 데이터베이스 클라이언트 라이브러리가 없으면 분할을 구현하는 경우 관리 코드를 작성하는데 많은 시간을 투자해야 합니다. 자세한 내용은 [분할된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md)를 참조하세요.

- **데이터 종속 라우팅**: 응용 프로그램에 들어오는 요청이 있다고 가정하겠습니다. 요청의 분할 키 값에 따라 응용 프로그램은 키 값을 기준으로 올바른 데이터베이스를 결정해야 합니다. 그런 다음 요청을 처리하는 데이터베이스에 대한 연결을 엽니다. 데이터 종속 라우팅은 응용 프로그램의 분할된 데이터베이스 맵에 대한 간편한 단일 호출을 사용하여 연결을 열 수 있는 기능을 제공합니다. 데이터 종속 라우팅은 지금은 탄력적 데이터베이스 클라이언트 라이브러리의 기능에서 담당하는 인프라 코드의 다른 영역이었습니다. 자세한 내용은 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)을 참조하세요.
- **MSQ(다중 분할된 데이터베이스 쿼리)**: 분할된 다중 데이터베이스 쿼리 요청은 한 요청이 여러(또는 모든) 분할된 데이터베이스와 관련된 경우 작동합니다. 다중 분할된 데이터베이스 쿼리는 모든 분할된 데이터베이스 또는 분할된 데이터베이스 집합에서 동일한 T-SQL 코드를 실행합니다. 참여하는 분할된 데이터베이스의 결과는 UNION ALL 의미 체계를 사용하는 전반적인 결과 집합으로 병합됩니다. 이 기능은 클라이언트 라이브러리를 통해 표시되며 연결 관리, 스레드 관리, 오류 처리, 중간 결과 처리 등의 여러 작업을 처리합니다. MSQ는 수백 개까지 분할된 데이터베이스를 쿼리할 수 있습니다. 자세한 내용은 [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md)를 참조하세요.

일반적으로, 고객의 탄력적 데이터베이스 도구 사용은 고유한 의미 체계를 가진 분할된 데이터베이스 간 작업과 반대로 분할된 데이터베이스 로컬 작업을 제출할 때 전체 T-SQL 기능을 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계
클라이언트 함수를 보여 주는 [샘플 앱](sql-database-elastic-scale-get-started.md) 을 시도하세요. 

라이브러리를 설치하려면 [탄력적 데이터베이스 클라이언트 라이브러리](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)로 이동하세요.

분할-병합 도구 사용에 대한 지침은 [분할-병합 도구 개요](sql-database-elastic-scale-overview-split-and-merge.md)를 참조하세요.

[탄력적 데이터베이스 클라이언트 라이브러리는 이제 소스가 공개되었습니다!](https://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)

[탄력적 쿼리](sql-database-elastic-query-overview.md)를 사용합니다.

라이브러리는 [GitHub](https://github.com/Azure/elastic-db-tools)에서 공개 소스 소프트웨어로 사용할 수 있습니다. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-database-client-library/glossary.png


