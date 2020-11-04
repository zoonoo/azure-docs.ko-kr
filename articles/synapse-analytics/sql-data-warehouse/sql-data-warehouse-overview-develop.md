---
title: Azure Synapse Analytics에서 전용 SQL 풀을 개발 하기 위한 리소스
description: Azure Synapse Analytics의 전용 SQL 풀에 대 한 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f158655cd7aeba647480325966c7f0cfcf65b13c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322145"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀에 대 한 디자인 결정 및 코딩 기술 

 이 문서에서는 Azure Synapse에서 전용 SQL 풀에 대 한 주요 디자인 결정, 권장 사항 및 코딩 기술을 더 잘 이해 하는 데 도움이 되는 추가 리소스를 찾을 수 있습니다.

## <a name="key-design-decisions"></a>주요 디자인 결정

다음 문서에서는 Azure Synapse에서 전용 SQL 풀 기능을 사용 하 여 분산 된 데이터 웨어하우스를 개발 하기 위한 개념 및 디자인 결정을 강조 합니다.

* [연결만](../sql/connect-overview.md)
* [동시성](resource-classes-for-workload-management.md)
* [트랜잭션을](sql-data-warehouse-develop-transactions.md)
* [사용자 정의 스키마](sql-data-warehouse-develop-user-defined-schemas.md)
* [테이블 배포](sql-data-warehouse-tables-distribute.md)
* [테이블 인덱스](sql-data-warehouse-tables-index.md)
* [테이블 파티션](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [통계](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>개발 권장 사항 및 코딩 기술

다음 문서에서는 전용 SQL 풀 개발을 위한 구체적인 코딩 기술, 팁 및 권장 사항을 제공 합니다.

* [저장 프로시저](sql-data-warehouse-develop-stored-procedures.md)
* [레이블에](sql-data-warehouse-develop-label.md)
* [뷰](performance-tuning-materialized-views.md)
* [임시 테이블](sql-data-warehouse-tables-temporary.md)
* [동적 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [루프로](sql-data-warehouse-develop-loops.md)
* [그룹화 방법 옵션](sql-data-warehouse-develop-group-by-options.md)
* [변수 할당](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>다음 단계

자세한 참조 정보는 [t-sql 문](sql-data-warehouse-reference-tsql-statements.md)을 참조 하세요.
