---
title: "데이터 형식 지침 - Azure SQL Data Warehouse | Microsoft Docs"
description: "SQL Data Warehouse와 호환되는 데이터 형식을 정의하는 권장 사항입니다."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 06/02/2017
ms.author: shigu;barbkess
ms.openlocfilehash: 5c24c71af16bd9851d9caf15fecfa4bb76f5f77e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="guidance-for-defining-data-types-for-tables-in-sql-data-warehouse"></a>SQL Data Warehouse의 테이블에 대한 데이터 형식을 정의하기 위한 지침
이러한 권장 사항을 사용하여 SQL Data Warehouse와 호환되는 테이블 데이터 형식을 정의합니다. 호환성 외에도 데이터 형식의 크기를 최소화하면 쿼리 성능이 향상됩니다.

SQL 데이터 웨어하우스는 가장 일반적으로 사용되는 데이터 형식을 지원합니다. 지원되는 데이터 형식의 목록은 CREATE TABLE 문에서 [데이터 형식](/sql/docs/t-sql/statements/create-table-azure-sql-data-warehouse.md#datatypes)을 참조하세요. 


## <a name="minimize-row-length"></a>행 길이 최소화
데이터 형식의 크기를 최소화하면 쿼리 성능을 향상시키는 행 길이를 줄입니다. 데이터에 대해 작동하는 가장 작은 데이터 형식을 사용합니다. 

- 문자 열을 큰 기본 길이로 정의하지 마세요. 예를 들어 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다. 
- VARCHAR만 필요한 경우 [NVARCHAR][NVARCHAR]를 사용하지 마세요.
- 가능한 경우 NVARCHAR(MAX) 또는 VARCHAR(MAX) 대신 NVARCHAR(4000) 또는 VARCHAR(8000)를 사용합니다.

Polybase를 사용하여 테이블을 로드하는 경우 정의된 데이터 행의 길이는 1MB를 초과할 수 없습니다. 가변 길이 데이터가 있는 행이 1MB를 초과하는 경우 행을 PolyBase가 아닌 BCP로 로드할 수 있습니다.

## <a name="identify-unsupported-data-types"></a>지원되지 않는 데이터 형식 식별
다른 SQL 데이터베이스에서 데이터베이스를 마이그레이션하려는 경우 SQL Data Warehouse에서 지원되지 않는 데이터 형식이 나타날 수 있습니다. 이 쿼리를 사용하여 기존 SQL 스키마에서 지원되지 않는 데이터 형식을 검색합니다.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>지원되지 않는 데이터 형식에 대한 해결 방법 사용

다음 목록은 SQL Data Warehouse에서 지원하지 않는 데이터 형식을 보여 주고 지원되지 않는 데이터 형식 대신 사용할 수 있는 대안을 제공합니다.

| 지원되지 않는 데이터 형식 | 해결 방법 |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |열을 강력한 형식의 열로 분할합니다. |
| [table][table] |임시 테이블로 변환합니다. |
| [timestamp][timestamp] |[datetime2][datetime2] 및 `CURRENT_TIMESTAMP` 함수를 사용하도록 코드 재작업을 수행합니다.  상수만 기본값으로 지원됩니다. 따라서 current_timestamp는 기본 제약 조건으로 정의할 수 없습니다. rowversion 값을 타임스탬프 형식의 열에서 마이그레이션해야 하는 경우, NOT NULL 또는 NULL 행 버전 값으로 [BINARY][BINARY](8) 또는 [VARBINARY][BINARY](8)를 사용합니다. |
| [xml][xml] |[varchar][varchar] |
| [사용자 정의 형식][user defined types] |가능하면 네이티브 데이터 형식으로 다시 변환합니다. |
| 기본값 | 기본값은 리터럴 및 상수만 지원합니다.  `GETDATE()` 또는 `CURRENT_TIMESTAMP`와 같은 명확하지 않은 식 또는 함수는 지원되지 않습니다. |


## <a name="next-steps"></a>다음 단계
자세한 내용은 다음을 참조하세요.

- [SQL Data Warehouse 모범 사례][SQL Data Warehouse Best Practices]
- [테이블 개요][Overview]
- [테이블 배포][Distribute]
- [테이블 인덱싱][Index]
- [테이블 분할][Partition]
- [테이블 통계 유지 관리][Statistics]
- [임시 테이블][Temporary]

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx
