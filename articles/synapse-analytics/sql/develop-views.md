---
title: SQL 풀을 사용하는 T-SQL 뷰
description: Azure Synapse Analytics의 전용 SQL 풀 및 서버리스 SQL 풀을 통해 T-SQL 뷰를 사용하고 솔루션을 개발하기 위한 팁입니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 9f52c3fd1284ce7e55680d051c5292361067fad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673996"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀 및 서버리스 SQL 풀을 사용한 T-SQL 뷰

본 문서에서는 Azure Synapse Analytics의 전용 SQL 풀 및 서버리스 SQL 풀을 통해 T-SQL 뷰를 사용하고 솔루션을 개발하기 위한 팁을 확인할 수 있습니다.

## <a name="why-use-views"></a>뷰를 사용하는 이유

뷰를 여러 가지 다양한 방법으로 사용하여 솔루션의 품질을 개선할 수 있습니다.  본 문서에서는 뷰를 통해 솔루션을 보완하는 방법에 대한 몇 가지 예를 중점적으로 설명하며 고려해야 할 제한 사항도 소개합니다.

### <a name="sql-pool---create-view"></a>SQL 풀 - 뷰 만들기

> [!NOTE]
> CREATE VIEW에 대한 구문은 이 문서에서 다루지 않습니다. 자세한 내용은 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

## <a name="architectural-abstraction"></a>아키텍처 추상화

일반적인 애플리케이션 패턴은 CTAS([CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)) 뒤에 데이터 로드 중 개체 이름 바꾸기 패턴을 사용하여 테이블을 다시 작성하는 것입니다.

다음 예제에서는 새 날짜 레코드를 날짜 차원에 추가합니다. 먼저 새 테이블 DimDate_New를 만든 다음, 이름을 바꾸어 원래 버전의 테이블을 바꾸는 방법을 확인합니다.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;
```

이 방법을 사용하면 뷰에서 테이블이 표시되었다가 사라질 수 있으며 "테이블이 없습니다" 오류 메시지가 표시될 수 있음에 유의합니다. 뷰는 기본 개체의 이름을 바꾸는 동안 일관성 있는 프레젠테이션 계층을 제공하는 데 사용할 수 있습니다.

뷰를 통해 데이터에 대한 액세스를 제공하면 기본 테이블이 사용자에게 표시되지 않아도 됩니다. 이 계층을 통해 사용자 환경의 일관성을 유지할 뿐만 아니라 분석 디자이너가 데이터 모델을 향상시킬 수 있습니다. 기본 테이블을 개선할 수 있으므로 설계자가 CTAS를 사용하여 데이터 로드 프로세스 중에 성능을 극대화할 수 있습니다.

## <a name="performance-optimization"></a>성능 최적화

뷰는 테이블 간에 성능 최적화된 조인을 적용하는 데도 활용할 수 있습니다. 예를 들어, 뷰는 데이터 이동을 최소화하는 조인 조건의 일부로 배포 중복 키를 통합할 수 있습니다.

특정 쿼리 또는 조인 힌트를 강요하는 것은 T-SQL 뷰를 사용하면서 얻을 수 있는 또 다른 장점입니다. 따라서 뷰 기능을 사용하면 조인이 항상 최적화됩니다. 조인을 위해 올바른 구분을 직접 기억할 필요가 없습니다.

## <a name="limitations"></a>제한 사항

Synapse SQL 뷰는 메타데이터로만 저장됩니다. 따라서 다음 옵션은 사용할 수 없습니다.

* 스키마 바인딩 옵션이 없습니다.
* 뷰를 통해 기본 테이블을 업데이트할 수 없습니다.
* 임시 테이블 뷰를 만들 수 없습니다.
* EXPAND / NOEXPAND 힌트를 지원하지 않습니다.
* Synapse SQL에 인덱싱된 뷰가 없습니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [Synapse SQL 개발 개요](develop-overview.md)를 참조하세요.



