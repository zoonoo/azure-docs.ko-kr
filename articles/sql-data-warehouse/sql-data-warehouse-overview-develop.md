---
title: "Azure의 데이터 웨어하우스 개발을 위한 리소스 | Microsoft Docs"
description: "SQL 데이터 웨어하우스에 대한 개발 개념, 디자인 결정, 권장 사항 및 코딩 기술입니다."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: develop
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: 37344b7916d8ceb2ad3b6a34df9fc8681af4dff7
ms.lasthandoff: 01/24/2017


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL 데이터 웨어하우스에 대한 디자인 결정 및 코딩 기술
SQL 데이터 웨어하우스에 대한 주요 디자인 결정, 권장 사항 및 코딩 기술을 더 잘 이해하려면 이러한 개발 문서를 살펴보세요.

## <a name="key-design-decisions"></a>주요 디자인 결정
다음 문서는 SQL 데이터 웨어하우스를 사용하여 분산된 데이터 웨어하우스를 개발하기 위해 이해해야 하는 일부 주요 개념과 설계 결정을 요약합니다.

* [연결][connections]
* [동시성][concurrency]
* [트랜잭션][transactions]
* [사용자 정의 스키마][user-defined schemas]
* [테이블 배포][table distribution]
* [테이블 인덱스][table indexes]
* [테이블 파티션][table partitions]
* [CTAS][CTAS]
* [통계][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>개발 권장 사항 및 코딩 기술
이러한 문서에는 SQL 데이터 웨어하우스 개발을 위한 구체적인 코딩 기술, 팁 및 권장 사항이 요약되어 있습니다.

* [저장 프로시저][stored procedures]
* [레이블][labels]
* [뷰][views]
* [임시 테이블][temporary tables]
* [동적 SQL][dynamic SQL]
* [반복][looping]
* [옵션으로 그룹화][group by options]
* [변수 할당][variable assignment]

## <a name="next-steps"></a>다음 단계
개발 문서들을 살펴본 후에는 [Transact-SQL 참조][Transact-SQL reference] 페이지에서 SQL Data Warehouse에 대해 지원되는 구문에 대한 자세한 내용을 살펴보세요.

<!--Image references-->

<!--Article references-->
[concurrency]: ./sql-data-warehouse-develop-concurrency.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md
[Transact-SQL reference]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->

