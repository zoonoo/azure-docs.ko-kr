---
title: 시냅스 SQL을 사용한 T-SQL 보기
description: T-SQL 뷰를 사용하고 Synapse SQL을 사용하여 솔루션을 개발하기 위한 팁입니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428655"
---
# <a name="t-sql-views-using-synapse-sql"></a>시냅스 SQL을 사용한 T-SQL 보기
이 문서에서는 T-SQL 보기를 사용하고 Synapse SQL을 사용하여 솔루션을 개발하기 위한 팁을 찾을 수 있습니다. 

## <a name="why-use-views"></a>뷰를 사용하는 이유

뷰를 여러 가지 다양한 방법으로 사용하여 솔루션의 품질을 개선할 수 있습니다.  이 문서에서는 뷰를 통해 솔루션을 보강하는 방법에 대한 몇 가지 예를 중시하고 고려해야 할 제한 사항을 포함합니다.

### <a name="sql-pool---create-view"></a>SQL 풀 - 뷰 만들기

> [!NOTE]
> **SQL 풀**: CREATE VIEW에 대한 구문은 이 문서에서 설명하지 않습니다. 자세한 내용은 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.

### <a name="sql-on-demand-preview---create-view"></a>SQL 온디맨드(미리 보기) - 보기 만들기

> [!NOTE]
> **SQL 주문형**: CREATE VIEW에 대한 구문은 이 문서에서 설명하지 않습니다. 자세한 내용은 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.

## <a name="architectural-abstraction"></a>아키텍처 추상화

일반적인 응용 프로그램 패턴은 데이터를 로드하는 동안 개체 이름 바꾸기 패턴다음에 CTAS(테이블 [AS SELECT)를 사용하여](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 테이블을 다시 만드는 것입니다.

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

이 방법을 사용하면 테이블이 사용자 보기에서 나타나고 사라질 수 있으며 "테이블이 존재하지 않습니다" 오류 메시지가 표시됩니다. 뷰를 사용하여 사용자에게 일관된 프레젠테이션 계층을 제공하고 기본 개체의 이름을 바꿀 수 있습니다.

뷰를 통해 데이터에 대한 액세스를 제공함으로써 사용자는 기본 테이블에 대한 가시성을 필요로 하지 않습니다. 이 계층은 일관된 사용자 환경 외에도 분석 디자이너가 데이터 모델을 발전시킬 수 있도록 합니다. 기본 테이블을 발전시킬 수 있는 기능은 디자이너가 CTAS를 사용하여 데이터 로드 프로세스 중에 성능을 극대화할 수 있도록 합니다.

## <a name="performance-optimization"></a>성능 최적화

뷰를 사용하여 테이블 간에 성능 최적화 된 조인을 적용할 수도 있습니다. 예를 들어, 뷰는 데이터 이동을 최소화하는 조인 조건의 일부로 배포 중복 키를 통합할 수 있습니다.

특정 쿼리를 적용하거나 힌트를 조인하는 것은 T-SQL 뷰를 사용하는 또 다른 이점입니다. 따라서 뷰 기능은 조인이 항상 최적의 방식으로 수행되도록 합니다. 사용자가 조인에 대한 올바른 구문들을 기억할 필요가 없습니다.

## <a name="limitations"></a>제한 사항

Synapse SQL의 보기는 메타데이터로만 저장됩니다. 따라서 다음 옵션을 사용할 수 없습니다.

* 스키마 바인딩 옵션이 없습니다.
* 뷰를 통해 기본 테이블을 업데이트할 수 없습니다.
* 임시 테이블에서 뷰를 만들 수 없습니다.
* 확장 / NOEXPAND 힌트에 대한 지원이 없습니다.
* Synapse SQL에는 인덱싱된 보기가 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 개발 팁은 [Synapse SQL 개발 개요를](develop-overview.md)참조하십시오.



