---
title: Synapse SQL을 사용 하는 t-sql 뷰
description: T-sql 뷰를 사용 하 고 Synapse SQL을 사용 하 여 솔루션을 개발 하기 위한 팁입니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81428655"
---
# <a name="t-sql-views-using-synapse-sql"></a>Synapse SQL을 사용 하는 t-sql 뷰
이 문서에서는 T-sql 뷰를 사용 하 고 Synapse SQL을 사용 하 여 솔루션을 개발 하기 위한 팁을 찾을 수 있습니다. 

## <a name="why-use-views"></a>뷰를 사용하는 이유

뷰를 여러 가지 다양한 방법으로 사용하여 솔루션의 품질을 개선할 수 있습니다.  이 문서에서는 보기를 사용 하 여 솔루션을 보강 하는 방법에 대 한 몇 가지 예제를 중점적으로 설명 하 고 고려해 야 할 제한 사항을 포함 합니다.

### <a name="sql-pool---create-view"></a>SQL 풀-create view

> [!NOTE]
> **SQL 풀**: CREATE VIEW 구문은이 문서에서 다루지 않습니다. 자세한 내용은 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.

### <a name="sql-on-demand-preview---create-view"></a>SQL 주문형 (미리 보기)-뷰 만들기

> [!NOTE]
> **SQL 주문형**: CREATE VIEW 구문은이 문서에서 다루지 않습니다. 자세한 내용은 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.

## <a name="architectural-abstraction"></a>아키텍처 추상화

일반적인 응용 프로그램 패턴은 데이터를 로드 하는 동안 개체 이름 바꾸기 패턴이 뒤에 오는 ctas ( [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) )를 사용 하 여 테이블을 다시 만드는 것입니다.

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

이 방법을 사용 하면 테이블이 사용자의 보기에서 사라지고 표시 되지 않고 "테이블이 없습니다." 라는 오류 메시지가 표시 될 수 있습니다. 뷰를 사용 하면 기본 개체의 이름을 바꾸는 동안 일관 된 프레젠테이션 계층을 사용자에 게 제공할 수 있습니다.

뷰를 통해 데이터에 대 한 액세스를 제공 함으로써 사용자는 기본 테이블을 볼 필요가 없습니다. 이 계층은 일관적인 사용자 환경 뿐만 아니라 분석 디자이너가 데이터 모델을 향상 시킬 수 있도록 합니다. 기본 테이블을 진화 하는 기능을 통해 디자이너는 CTAS를 사용 하 여 데이터 로드 프로세스 중에 성능을 최대화할 수 있습니다.

## <a name="performance-optimization"></a>성능 최적화

뷰를 사용 하 여 테이블 간에 성능 최적화 조인을 적용할 수도 있습니다. 예를 들어, 뷰는 데이터 이동을 최소화하는 조인 조건의 일부로 배포 중복 키를 통합할 수 있습니다.

특정 쿼리 또는 조인 힌트를 강제 적용 하는 것은 T-sql 뷰를 사용 하는 또 다른 장점입니다. 따라서 뷰 기능을 사용 하면 조인이 항상 최적의 방식으로 수행 됩니다. 사용자가 조인을 위해 올바른 구문을 기억할 필요가 없습니다.

## <a name="limitations"></a>제한 사항

Synapse SQL의 뷰는 메타 데이터로만 저장 됩니다. 따라서 다음 옵션을 사용할 수 없습니다.

* 스키마 바인딩 옵션이 없습니다.
* 뷰를 통해 기본 테이블을 업데이트할 수 없습니다.
* 임시 테이블에 대 한 뷰를 만들 수 없습니다.
* EXPAND/NOEXPAND 힌트는 지원 되지 않습니다.
* Synapse SQL에 인덱싱된 뷰가 없습니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [Synapse SQL 개발 개요](develop-overview.md)를 참조하세요.



