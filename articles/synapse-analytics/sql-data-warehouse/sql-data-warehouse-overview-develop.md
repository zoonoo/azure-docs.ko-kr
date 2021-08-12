---
title: Azure Synapse Analytics에서 전용 SQL 풀(이전의 SQL DW)을 개발하기 위한 리소스
description: Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW)에 대한 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6b34c70b453c26fe27a51e1aa802564864640cb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96453680"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀(이전의 SQL DW)에 대한 디자인 결정 및 코딩 기술 

 이 문서에서는 Azure Synapse의 전용 SQL 풀(이전의 SQL DW)에 대한 주요 디자인 결정, 권장 사항 및 코딩 기술을 더 잘 이해하는 데 도움이 되는 추가 리소스를 찾을 수 있습니다.

## <a name="key-design-decisions"></a>주요 디자인 결정

다음 문서에서는 Azure Synapse의 전용 SQL 풀(이전의 SQL DW) 기능을 사용하여 분산 데이터 웨어하우스를 개발하기 위한 개념과 디자인 결정 사항을 중점적으로 설명합니다.

* [connections](sql-data-warehouse-connect-overview.md)
* [동시성](resource-classes-for-workload-management.md)
* [트랜잭션](sql-data-warehouse-develop-transactions.md)
* [사용자 정의 스키마](sql-data-warehouse-develop-user-defined-schemas.md)
* [테이블 배포](sql-data-warehouse-tables-distribute.md)
* [테이블 인덱스](sql-data-warehouse-tables-index.md)
* [테이블 파티션](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [통계](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>개발 권장 사항 및 코딩 기술

다음 문서에는 전용 SQL 풀(이전의 SQL DW)을 개발하기 위한 특정 코딩 기술과 팁, 권장 사항이 나와 있습니다.

* [저장 프로시저](sql-data-warehouse-develop-stored-procedures.md)
* [레이블](sql-data-warehouse-develop-label.md)
* [뷰](performance-tuning-materialized-views.md)
* [임시 테이블](sql-data-warehouse-tables-temporary.md)
* [동적 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [반복](sql-data-warehouse-develop-loops.md)
* [옵션으로 그룹화](sql-data-warehouse-develop-group-by-options.md)
* [변수 할당](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>다음 단계

자세한 참조 정보는 [T-SQL 문](sql-data-warehouse-reference-tsql-statements.md)을 참조하세요.
