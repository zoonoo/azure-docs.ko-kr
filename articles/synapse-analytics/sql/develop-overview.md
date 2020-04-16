---
title: 시냅스 SQL 기능 개발을 위한 리소스
description: Synapse SQL을 위한 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429019"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Azure Synapse 분석에서 시냅스 SQL 기능에 대한 설계 결정 및 코딩 기술
이 문서에서는 Synapse SQL의 SQL 풀 및 SQL 온디맨드(미리 보기) 함수에 대한 리소스 목록을 찾을 수 있습니다. 권장되는 문서는 주요 디자인 결정 및 개발 및 코딩 기술의 두 부분으로 나뉩니다.

이 문서의 목표는 Synapse 분석 내에서 Synapse SQL 구성 요소에 대한 최적의 기술 접근 방식을 개발하는 데 도움이 됩니다.

## <a name="key-design-decisions"></a>주요 디자인 결정
아래 문서에서는 Synapse SQL 개발에 대한 개념 및 디자인 결정을 강조합니다.

|                                                          |   SQL 풀   | SQL 온디맨드 |
| -----------------------------------------------------    | ---- | ---- |
| [연결](connect-overview.md)                    | 예 | 예 |
| [리소스 클래스 및 동시성](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 예    | 예 |
| [트랜잭션](develop-transactions.md)              | 예 | 예 |
| [사용자 정의 스키마](develop-user-defined-schemas.md) | 예 | 예 |
| [테이블 분포](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | 예 | 예 |
| [테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | 예 | 예 |
| [테이블 파티션](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | 예 | 예 |
| [통계](develop-tables-statistics.md)            | 예 | 예 |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | 예 | 예 |
| [외부 테이블](develop-tables-external-tables.md) | 예 | 예 |
| [세타스 (주)](develop-tables-cetas.md)                     | 예 | 예 |


## <a name="recommendations"></a>권장 사항

아래에서 특정 코딩 기술, 팁 및 개발 권장 사항을 강조하는 필수 문서를 찾을 수 있습니다.

|                                            | SQL 풀 | SQL 온디맨드 |
| ------------------------------------------ | ------------------ | ----------------------- |
| [저장 프로시저](develop-stored-procedures.md)  | 예                | 예                      |
| [레이블](develop-label.md)                           | 예                | 예                      |
| [보기](develop-views.md)                             | 예                | 예                     |
| [임시 테이블](develop-tables-temporary.md)       | 예                | 예                     |
| [동적 SQL](develop-dynamic-sql.md)                 | 예                | 예                     |
| [반복](develop-loops.md)                         | 예                | 예                     |
| [옵션으로 그룹화](develop-group-by-options.md)       | 예                | 예                      |
| [변수 할당](develop-variable-assignment.md) | 예                | 예                     |

## <a name="next-steps"></a>다음 단계
자세한 참조 정보는 [SQL 풀 T-SQL 문을](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)참조하십시오.

