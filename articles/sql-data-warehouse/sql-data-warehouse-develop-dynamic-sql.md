---
title: "SQL Data Warehouse의 동적 SQL | Microsoft Docs"
description: "솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 동적 SQL 사용 팁."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: a948c2c3-3cd1-4373-90a9-79e59414b778
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29228676373aee8dbc7b1b2a7d92ffc978333804
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL 데이터 웨어하우스의 동적 SQL
SQL 데이터 웨어하우스용 응용 프로그램 코드를 개발할 때 유연하고 일반적인 모듈식 솔루션을 제공하기 위해 동적 SQL을 사용해야 할 수 있습니다. SQL 데이터 웨어하우스는 현재 Blob 데이터 형식을 지원하지 않습니다. 따라서 Blob 유형에 varchar(max) 및 nvarchar(max) 형식이 모두 포함되므로 문자열 크기가 제한될 수 있습니다. 매우 큰 문자열을 작성할 때 이러한 형식을 응용 프로그램 코드에 사용하는 경우 코드를 청크로 나누고 EXEC 문을 대신 사용해야 합니다.

간단한 예는 다음과 같습니다.

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

문자열이 짧은 경우 일반적으로 [sp_executesql][sp_executesql]을 사용할 수 있습니다.

> [!NOTE]
> 동적 SQL로 실행되는 문은 모든 TSQL 유효성 검사 규칙에 적용됩니다.
> 
> 

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요][development overview]를 참조하세요.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
