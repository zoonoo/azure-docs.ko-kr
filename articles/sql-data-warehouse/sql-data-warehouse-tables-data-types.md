---
title: "SQL Data Warehouse 테이블의 데이터 형식 | Microsoft Docs"
description: "Azure SQL 데이터 웨어하우스 테이블에 대한 데이터 형식으로 시작"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 7757de96601c426ff07e94cfa0c12d4dec8f06f5
ms.lasthandoff: 03/22/2017


---
# <a name="data-types-for-tables-in-sql-data-warehouse"></a>SQL 데이터 웨어하우스 테이블의 데이터 형식
> [!div class="op_single_selector"]
> * [개요][Overview]
> * [데이터 형식][Data Types]
> * [배포][Distribute]
> * [인덱스][Index]
> * [파티션][Partition]
> * [통계][Statistics]
> * [임시][Temporary]
> 
> 

SQL 데이터 웨어하우스는 가장 일반적으로 사용되는 데이터 형식을 지원합니다.  다음은 SQL 데이터 웨어하우스에서 지원하는 데이터 형식 목록입니다.  데이터 형식 지원에 대한 자세한 내용은 [테이블 만들기][create table]를 참조하세요.

| **지원되는 데이터 형식** |  |  |
| --- | --- | --- |
| [bigint][bigint] |[decimal][decimal] |[smallint][smallint] |
| [binary][binary] |[float][float] |[smallmoney][smallmoney] |
| [bit][bit] |[int][int] |[sysname][sysname] |
| [char][char] |[money][money] |[time][time] |
| [date][date] |[nchar][nchar] |[tinyint][tinyint] |
| [datetime][datetime] |[nvarchar][nvarchar] |[uniqueidentifier][uniqueidentifier] |
| [datetime2][datetime2] |[real][real] |[varbinary][varbinary] |
| [datetimeoffset][datetimeoffset] |[smalldatetime][smalldatetime] |[varchar][varchar] |

## <a name="data-type-best-practices"></a>데이터 형식 모범 사례
 열 형식을 정의할 때 데이터를 지원할 가장 작은 데이터 형식을 사용하면 쿼리 성능이 향상됩니다. 이것은 CHAR 및 VARCHAR 열에 대해 특히 중요합니다. 열에서 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다. 모든 문자 열을 큰 기본 길이로 정의하지 마세요. 또한 반드시 필요한 경우에는 열을 [NVARCHAR][NVARCHAR]을 사용하는 것보다 VARCHAR로 정의하세요.  가능한 경우 NVARCHAR(MAX) 또는 VARCHAR(MAX) 대신 NVARCHAR(4000) 또는 VARCHAR(8000)를 사용하세요.

## <a name="polybase-limitation"></a>Polybase 제한
Polybase를 사용하여 테이블을 로드하는 경우 데이터의 길이는 1MB를 초과하지 않아야 합니다.  이 너비를 초과할 수 있는 가변 길이 데이터로 행을 정의할 수 있고 BCP를 사용하여 행을 로드할 수 있는 반면 이 데이터를 로드하는 데 Polybase를 사용할 수 없습니다.  

## <a name="unsupported-data-types"></a>지원되지 않는 데이터 형식
Azure SQL 데이터베이스 등의 다른 SQL 플랫폼에서 데이터베이스를 마이그레이션하려는 경우 마이그레이션할 때 SQL 데이터 웨어하우스에서 지원되지 않는 일부 데이터 형식이 나타날 수 있습니다.  다음은 지원하지 않는 데이터 형식 대신 사용할 수 있는 일부 대안 및 지원되지 않는 데이터 형식입니다.

| 데이터 형식 | 해결 방법 |
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
| [사용자 정의 형식][user defined types] |가능한 경우 해당 네이티브 형식으로 다시 변환합니다. |
| 기본값 |기본값은 리터럴 및 상수만 지원합니다.  `GETDATE()` 또는 `CURRENT_TIMESTAMP`와 같은 명확하지 않은 식 또는 함수는 지원되지 않습니다. |

아래의 SQL은 Azure SQL 데이터 웨어하우스에서 지원되지 않는 열을 식별하기 위해 현재 SQL 데이터베이스에 대해 실행할 수 있습니다.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>다음 단계
자세히 알아보려면 [테이블 개요][Overview], [테이블 배포][Distribute], [테이블 인덱싱][Index], [테이블 분할][Partition], [테이블 통계 유지 관리][Statistics] 및 [임시 테이블][Temporary]에 대한 문서를 참조하세요.  모범 사례에 대한 자세한 내용은 [SQL Data Warehouse 모범 사례][SQL Data Warehouse Best Practices]를 참조하세요.

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

