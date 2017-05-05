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
ms.custom: migrate
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0630f43642a0be98c470032d32b74ca14ee144e5
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>SQL 데이터 웨어하우스로 스키마 마이그레이션
다음 요약은 SQL Server와 SQL 데이터 웨어하우스 간의 차이점을 이해하여 데이터베이스를 마이그레이션하는 데 도움이 됩니다.

## <a name="table-migration"></a>테이블 마이그레이션
테이블을 마이그레이션할 때는 SQL Data Warehouse 테이블의 테이블 기능을 숙지해야 합니다.  먼저 [테이블 개요][table overview]를 살펴보는 것이 좋습니다.  이 문서에서는 테이블 통계, 분산, 분할, 인덱싱 등 테이블을 만들 때 고려해야 하는 가장 중요한 사항을 소개합니다.  또한 몇 가지 [지원되지 않는 테이블 기능][unsupported table features] 및 그와 관련된 해결 방법에 대해서도 다룹니다.

SQL Data Warehouse는 일반적인 비즈니스 데이터 형식을 지원합니다.  지원되는 데이터 형식 및 [지원되지 않는 데이터 형식][unsupported data types]의 목록은 [데이터 형식][data types] 문서를 참조하세요.  또한 [데이터 형식][data types] 문서는 [지원되지 않는 데이터 형식][unsupported data types]을 식별하는 쿼리를 포함합니다.  데이터 형식을 변환할 때는 반드시 [데이터 형식 모범 사례][data type best practices]를 확인하세요.

## <a name="next-steps"></a>다음 단계
SQL 데이터 웨어하우스로 데이터베이스 스키마를 성공적으로 마이그레이션한 후에 다음 문서 중 하나를 진행할 수 있습니다.

* [데이터 마이그레이션][Migrate your data]
* [코드 마이그레이션][Migrate your code]

SQL Data Warehouse 모범 사례에 대한 자세한 내용은 [모범 사례][best practices] 문서를 참조하세요.

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[data type best practices]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

