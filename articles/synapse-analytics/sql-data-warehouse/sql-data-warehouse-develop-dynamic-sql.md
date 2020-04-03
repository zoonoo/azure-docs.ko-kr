---
title: 동적 SQL 사용
description: Synapse SQL 풀에서 동적 SQL을 사용하는 개발 솔루션에 대한 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5a285c273a0bc590a9f5b4ade782f2195a361cd6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619028"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>시냅스 SQL 풀의 동적 SQL
이 문서에는 SQL 풀에서 동적 SQL을 사용하는 개발 솔루션에 대한 팁이 포함되어 있습니다.

## <a name="dynamic-sql-example"></a>동적 SQL 예제

SQL 풀용 응용 프로그램 코드를 개발할 때 동적 SQL을 사용하여 유연하고 일반적이며 모듈식 솔루션을 제공해야 할 수 있습니다. SQL 풀은 현재 Blob 데이터 형식을 지원하지 않습니다. 

Blob 데이터 형식을 지원하지 않으면 Blob 데이터 형식에 varchar(max) 및 nvarchar(max) 형식이 둘 다 포함되므로 문자열 크기가 제한될 수 있습니다. 

응용 프로그램 코드에서 이러한 형식을 사용하여 큰 문자열을 빌드한 경우 코드를 청크로 나누고 대신 EXEC 문을 사용해야 합니다.

간단한 예는 다음과 같습니다.

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

문자열이 짧은 경우 일반적으로 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)을 사용할 수 있습니다.

> [!NOTE]
> 동적 SQL로 실행되는 문은 여전히 모든 T-SQL 유효성 검사 규칙의 적용을 받습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.

