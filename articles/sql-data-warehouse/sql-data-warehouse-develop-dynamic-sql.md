---
title: Azure SQL Data Warehouse의 동적 SQL 사용 | Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse의 동적 SQL 사용 팁.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 567637cab1c983992b08f65352ab9a92bd448c5a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861818"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL Data Warehouse의 동적 SQL
솔루션 개발을 위한 Azure SQL Data Warehouse의 동적 SQL 사용 팁.

## <a name="dynamic-sql-example"></a>동적 SQL 예제

SQL Data Warehouse용 애플리케이션 코드를 개발할 때 유연하고 일반적인 모듈식 솔루션을 제공하기 위해 동적 SQL을 사용해야 할 수 있습니다. SQL Data Warehouse는 현재 Blob 데이터 형식을 지원하지 않습니다. Blob 데이터 형식을 지원하지 않으면 Blob 데이터 형식에 varchar(max) 및 nvarchar(max) 형식이 둘 다 포함되므로 문자열 크기가 제한될 수 있습니다. 매우 큰 문자열을 작성할 때 이러한 형식을 애플리케이션 코드에 사용하는 경우 코드를 청크로 나누고 EXEC 문을 대신 사용해야 합니다.

간단한 예는 다음과 같습니다.

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

문자열이 짧은 경우 일반적으로 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)을 사용할 수 있습니다.

> [!NOTE]
> 동적 SQL로 실행되는 문은 모든 TSQL 유효성 검사 규칙에 적용됩니다.
> 
> 

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.

