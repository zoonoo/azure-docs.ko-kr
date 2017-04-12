---
title: "SQL Data Warehouse의 변수 할당 | Microsoft Docs"
description: "솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Transact-SQL 변수 할당을 위한 팁"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 81ddc7cf-a6ba-4585-91a3-b6ea50f49227
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 045d5148cd3f12dac63c961ccf7c953d355ed725
ms.lasthandoff: 04/03/2017


---
# <a name="assign-variables-in-sql-data-warehouse"></a>SQL 데이터 웨어하우스의 변수 할당
SQL 데이터 웨어하우스의 변수는 `DECLARE` 문 또는 `SET` 문을 사용하여 설정됩니다.

다음 모두는 다양한 변수를 설정하는 완벽하게 유효한 방법입니다.

## <a name="setting-variables-with-declare"></a>DECLARE를 사용하여 변수 설정
DECLARE를 사용한 변수 초기화는 SQL 데이터 웨어하우스의 변수 값을 설정하는 가장 유연한 방법 중 하나입니다.

```sql
DECLARE @v  int = 0
;
```

한 번에 둘 이상의 변수를 설정하려면 DECLARE를 사용할 수도 있습니다. 다음을 수행하려면 `SELECT` 또는 `UPDATE`를 사용할 수 없습니다.

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

초기화하여 동일한 DECLARE 문에서 변수를 사용할 수 없습니다. 지점을 설명하기 위해 아래 예에서는 @p1이 동일한 DECLARE 문에서 시작되고 사용되기 때문에 허용되지 **않습니다**. 그러면 오류가 발생합니다.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>SET을 사용하여 값 설정
집합은 단일 변수를 설정하기 위한 매우 일반적인 방법입니다.

아래 예제 모두는 SET을 사용하여 변수를 설정하는 올바른 방법입니다.

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

SET을 사용하여 한 번에 하나의 변수만 설정할 수 있습니다. 그러나 위에서 설명한 것처럼 복합 연산자가 허용됩니다.

## <a name="limitations"></a>제한 사항
변수 할당에 SELECT 또는 UPDATE를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요][development overview]를 참조하세요.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

