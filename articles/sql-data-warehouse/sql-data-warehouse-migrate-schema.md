---
title: "SQL Data Warehouse로 스키마 마이그레이션| Microsoft Docs"
description: "솔루션 개발을 위한 Azure SQL 데이터 웨어하우스로 스키마를 마이그레이션하기 위한 팁"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1ac4db6d9404567be0d3d12f91a96fa4e31ef3bf


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>SQL 데이터 웨어하우스로 스키마 마이그레이션
다음 요약은 SQL Server와 SQL 데이터 웨어하우스 간의 차이점을 이해하여 데이터베이스를 마이그레이션하는 데 도움이 됩니다.

## <a name="table-migration"></a>테이블 마이그레이션
테이블을 마이그레이션할 때는 SQL Data Warehouse 테이블의 테이블 기능을 숙지해야 합니다.  먼저 [테이블 개요][테이블 개요]를 살펴보는 것이 좋습니다.  이 문서에서는 테이블 통계, 분산, 분할, 인덱싱 등 테이블을 만들 때 고려해야 하는 가장 중요한 사항을 소개합니다.  또한 몇 가지 [지원되지 않는 테이블 기능][지원되지 않는 테이블 기능] 및 그와 관련된 해결 방법에 대해서도 다룹니다.

SQL Data Warehouse는 일반적인 비즈니스 데이터 형식을 지원합니다.  지원되는 데이터 형식 및 [데이터 형식][데이터 형식]의 목록은 [데이터 형식][데이터 형식] 문서를 참조하세요.  또한 [데이터 형식][데이터 형식] 문서는 [데이터 형식][데이터 형식]을 식별하는 쿼리를 포함합니다.  데이터 형식을 변환할 때는 반드시 [데이터 형식 모범 사례][데이터 형식 모범 사례]를 확인하세요.

## <a name="next-steps"></a>다음 단계
SQL 데이터 웨어하우스로 데이터베이스 스키마를 성공적으로 마이그레이션한 후에 다음 문서 중 하나를 진행할 수 있습니다.

* [데이터 마이그레이션][데이터 마이그레이션]
* [코드 마이그레이션][코드 마이그레이션]

SQL Data Warehouse 모범 사례에 대한 자세한 내용은 [모범 사례][모범 사례] 문서를 참조하세요.

<!--Image references-->

<!--Article references-->
[코드 마이그레이션]: ./sql-data-warehouse-migrate-code.md
[데이터 마이그레이션]: ./sql-data-warehouse-migrate-data.md
[모범 사례]: ./sql-data-warehouse-best-practices.md
[테이블 개요]: ./sql-data-warehouse-tables-overview.md
[지원되지 않는 테이블 기능]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[데이터 형식]: ./sql-data-warehouse-tables-data-types.md
[데이터 형식]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[데이터 형식 모범 사례]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


