---
title: 동적 SQL 사용
description: Azure Synapse Analytics의 전용 SQL 풀에 대해 동적 SQL을 사용 하는 개발 솔루션에 대 한 팁입니다.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 52bc7bdc63f754d52bf4a69097c1dd309a6dc3ec
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462780"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀에 대 한 동적 SQL

이 문서에는 전용 SQL 풀에서 동적 SQL을 사용 하는 개발 솔루션에 대 한 팁이 포함 되어 있습니다.

## <a name="dynamic-sql-example"></a>동적 SQL 예제

전용 SQL 풀에 대해 응용 프로그램 코드를 개발할 때 유연 하 고 일반적인 모듈식 솔루션을 제공 하기 위해 동적 SQL을 사용 해야 할 수 있습니다. 현재 전용 SQL 풀은 blob 데이터 형식을 지원 하지 않습니다.

Blob 데이터 형식을 지원하지 않으면 Blob 데이터 형식에 varchar(max) 및 nvarchar(max) 형식이 둘 다 포함되므로 문자열 크기가 제한될 수 있습니다.

응용 프로그램 코드에서 이러한 형식을 사용 하 여 대량의 문자열을 작성 한 경우 코드를 청크로 나누고 EXEC 문을 대신 사용 해야 합니다.

간단한 예는 다음과 같습니다.

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

문자열이 짧은 경우 일반적으로 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용할 수 있습니다.

> [!NOTE]
> 동적 SQL로 실행 되는 문에는 여전히 모든 T-sql 유효성 검사 규칙이 적용 됩니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
