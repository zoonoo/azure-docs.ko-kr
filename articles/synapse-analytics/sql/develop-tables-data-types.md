---
title: Synapse SQL의 테이블 데이터 형식
description: Synapse SQL에서 테이블 데이터 형식을 정의 하기 위한 권장 사항입니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 5a831d79d315cb71712515eed45d9a679d307e06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760923"
---
# <a name="table-data-types-in-synapse-sql"></a>Synapse SQL의 테이블 데이터 형식

이 문서에서는 Synapse SQL에서 테이블 데이터 형식을 정의 하기 위한 권장 사항을 찾을 수 있습니다. 

## <a name="data-types"></a>데이터 형식

Synapse SQL은 가장 일반적으로 사용 되는 데이터 형식을 지원 합니다. 지원되는 데이터 형식의 목록은 CREATE TABLE 문에서 [데이터 형식](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true)을 참조하세요. 

## <a name="minimize-row-length"></a>행 길이 최소화

데이터 형식의 크기를 최소화하면 쿼리 성능을 향상시키는 행 길이를 줄입니다. 데이터에 대해 작동하는 가장 작은 데이터 형식을 사용합니다.

- 문자 열을 큰 기본 길이로 정의하지 마세요. 예를 들어 가장 긴 값이 25자인 경우 열을 VARCHAR(25)로 정의합니다.
- VARCHAR만 필요한 경우 [NVARCHAR][NVARCHAR]를 사용하지 마세요.
- 가능한 경우 NVARCHAR(MAX) 또는 VARCHAR(MAX) 대신 NVARCHAR(4000) 또는 VARCHAR(8000)를 사용합니다.

> [!NOTE]
> PolyBase 외부 테이블을 사용 하 여 SQL 풀 테이블을 로드 하는 경우 정의 된 테이블 행의 길이는 1mb를 초과할 수 없습니다. 가변 길이 데이터가 있는 행이 1MB를 초과하는 경우 행을 PolyBase가 아닌 BCP로 로드할 수 있습니다.

## <a name="identify-unsupported-data-types"></a>지원되지 않는 데이터 형식 식별

다른 SQL database에서 데이터베이스를 마이그레이션하는 경우 Synapse SQL에서 지원 되지 않는 데이터 형식이 발생할 수 있습니다. 이 쿼리를 사용하여 기존 SQL 스키마에서 지원되지 않는 데이터 형식을 검색합니다.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>지원되지 않는 데이터 형식에 대한 해결 방법

다음 목록에서는 Synapse SQL이 지원 하지 않는 데이터 형식을 보여 주고 지원 되지 않는 데이터 형식 대신 사용할 수 있는 대안을 제공 합니다.

| 지원되지 않는 데이터 형식 | 해결 방법 |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |열을 강력한 형식의 열로 분할합니다. |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |SQL 풀을 사용 하는 경우 임시 테이블로 변환할 수 있습니다. SQL (미리 보기)를 사용 하는 경우 [CETAS](../sql/develop-tables-cetas.md)를 사용 하 여 저장소에 데이터를 저장 하는 것이 좋습니다. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |[datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 및 [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 함수를 사용하도록 코드 재작업을 수행합니다. 상수만 기본값으로 지원됩니다. 따라서 current_timestamp는 기본 제약 조건으로 정의할 수 없습니다. 행 버전 값을 타임스탬프 형식의 열에서 마이그레이션해야 하는 경우, NOT NULL 또는 NULL 행 버전 값으로 [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) 또는 [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) 를 사용합니다. |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [사용자 정의 형식](/sql/relational-databases/native-client/features/using-user-defined-types) |가능하면 네이티브 데이터 형식으로 다시 변환합니다. |
| 기본값 | 기본값은 리터럴 및 상수만 지원합니다. |

## <a name="next-steps"></a>다음 단계

테이블 개발에 대한 자세한 내용은 [테이블 개요](develop-overview.md)를 참조하세요.
