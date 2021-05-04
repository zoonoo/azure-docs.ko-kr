---
title: Synapse SQL 기능 개발용 리소스
description: Synapse SQL의 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술입니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4d842414d3046692c982ca3203957a96f8a01b37
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377333"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Synapse SQL 기능을 위한 디자인 결정 및 코딩 기술
이 문서에서는 전용 SQL 풀의 리소스 목록과 Synapse SQL의 서버리스 SQL 풀 함수를 확인합니다. 권장 문서는 주요 디자인 결정과 개발 및 코딩 기술의 두 섹션으로 나뉘어 있습니다.

이러한 문서의 목적은 Azure Synapse Analytics 내에서 Synapse SQL 구성 요소에 대한 최적의 기술 방법을 개발하는 데 도움을 주는 것입니다.

## <a name="key-design-decisions"></a>주요 디자인 결정
다음 문서에서는 Synapse SQL 개발에 대한 개념 및 디자인 결정에 대해 집중적으로 다룹니다.

| 아티클 | 전용 SQL 풀 | 서버리스 SQL 풀 |
| ------- | -------- | ------------- |
| [연결](connect-overview.md)                    | 예 | 예 |
| [리소스 클래스 및 동시성](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 예    | 예 |
| [트랜잭션](develop-transactions.md)              | 예 | 예 |
| [사용자 정의 스키마](develop-user-defined-schemas.md) | 예 | 예 |
| [테이블 분산](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | 예 | 예 |
| [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | 예 | 예 |
| [테이블 파티션](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | 예 | 예 |
| [통계](develop-tables-statistics.md)            | 예 | 예 |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | 예 | 예 |
| [외부 테이블](develop-tables-external-tables.md) | 예 | 예 |
| [CETAS](develop-tables-cetas.md)                     | 예 | 예 |


## <a name="recommendations"></a>권장 사항

아래에는 개발을 위한 특정 코딩 기술, 팁 및 권장 사항을 집중적으로 다루는 필수 문서가 나와 있습니다.

| 아티클 | 전용 SQL 풀 | 서버리스 SQL 풀 |
| ------- | -------- | ------------- |
| [저장 프로시저](develop-stored-procedures.md)  | 예                | 예                      |
| [레이블](develop-label.md)                           | 예                | 예                      |
| [뷰](develop-views.md)                             | 예                | 예                     |
| [임시 테이블](develop-tables-temporary.md)       | 예                | 예                     |
| [동적 SQL](develop-dynamic-sql.md)                 | 예                | 예                     |
| [반복](develop-loops.md)                         | 예                | 예                     |
| [옵션으로 그룹화](develop-group-by-options.md)       | 예                | 예                      |
| [변수 할당](develop-variable-assignment.md) | 예                | 예                     |

## <a name="next-steps"></a>다음 단계
자세한 참조 정보는 [SQL 풀 T-SQL 문](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)을 참조하세요.

