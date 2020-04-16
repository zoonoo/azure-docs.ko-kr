---
title: 시냅스 SQL로 변수 할당
description: 이 문서에서는 Synapse SQL을 사용하여 T-SQL 변수를 할당하는 팁을 찾을 수 있습니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428668"
---
# <a name="assigning-variables-with-synapse-sql"></a>시냅스 SQL로 변수 할당

이 문서에서는 Synapse SQL을 사용하여 T-SQL 변수를 할당하는 팁을 찾을 수 있습니다.

## <a name="setting-variables-with-declare"></a>DECLARE를 사용하여 변수 설정

Synapse SQL의 변수는 `DECLARE` 명령문 또는 `SET` 문을 사용하여 설정됩니다. DECLARE를 사용하여 변수를 초기화하는 것은 Synapse SQL에서 변수 값을 설정하는 가장 유연한 방법 중 하나입니다.

```sql
DECLARE @v  int = 0
;
```

한 번에 둘 이상의 변수를 설정하려면 DECLARE를 사용할 수도 있습니다. SELECT 또는 UPDATE를 사용하여 다음을 수행할 수 없습니다.

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

동일한 DECLARE 문에서 변수를 초기화하고 사용할 수 없습니다. 예를 들어, 다음 예제는 초기화 하 고 동일한 DECLARE 문에 사용 되기 때문에 *@p1* 허용 되지 않습니다. 다음 예제에서는 오류가 발생합니다.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>SET을 사용하여 값 설정

SET은 단일 변수를 설정하기 위한 일반적인 방법입니다.

다음 명령문은 SET을 사용하여 변수를 설정하는 모든 유효한 방법입니다.

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

SET을 사용하여 한 번에 하나의 변수만 설정할 수 있습니다. 그러나 복합 연산자는 허용됩니다.

## <a name="limitations"></a>제한 사항

변수 할당에 UPDATE를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 개발 팁은 [Synapse SQL 개발 개요](develop-overview.md) 문서를 참조하세요.