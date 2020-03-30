---
title: Azure 시냅스 분석에서 데이터 웨어하우스 개발을 위한 리소스
description: SQL Data Warehouse에 대한 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa0f5fd631dfa3e4deca4853c27a667fcf312fec
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350288"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 데이터 웨어하우스에 대한 설계 의사 결정 및 코딩 기술 
 이 문서에서는 Azure Synapse의 데이터 웨어하우스에 대한 주요 디자인 결정, 권장 사항 및 코딩 기술을 더 잘 이해하는 데 도움이 되는 추가 리소스를 찾을 수 있습니다.

## <a name="key-design-decisions"></a>주요 디자인 결정
다음 문서에서는 Azure Synapse의 SQL Analytics 기능을 사용하여 분산 데이터 웨어하우스를 개발하기 위한 개념 및 디자인 결정을 강조 표시합니다.

* [연결](sql-data-warehouse-connect-overview.md)
* [동시성](resource-classes-for-workload-management.md)
* [트랜잭션을](sql-data-warehouse-develop-transactions.md)
* [사용자 정의 스키마](sql-data-warehouse-develop-user-defined-schemas.md)
* [테이블 배포](sql-data-warehouse-tables-distribute.md)
* [테이블 인덱스](sql-data-warehouse-tables-index.md)
* [테이블 파티션](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [통계](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>개발 권장 사항 및 코딩 기술
다음 문서에서는 SQL Analytics를 사용하여 데이터 웨어하우스를 개발하기 위한 특정 코딩 기술, 팁 및 권장 사항을 제공합니다.

* [저장 절차](sql-data-warehouse-develop-stored-procedures.md)
* [레이블](sql-data-warehouse-develop-label.md)
* [뷰](sql-data-warehouse-develop-views.md)
* [임시 테이블](sql-data-warehouse-tables-temporary.md)
* [동적 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [반복](sql-data-warehouse-develop-loops.md)
* [옵션으로 그룹화](sql-data-warehouse-develop-group-by-options.md)
* [변수 할당](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>다음 단계
자세한 참조 정보는 [T-SQL 문을](sql-data-warehouse-reference-tsql-statements.md)참조하십시오.
