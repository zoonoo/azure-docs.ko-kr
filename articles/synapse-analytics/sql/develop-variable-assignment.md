---
title: Synapse SQL을 사용 하 여 변수 할당
description: 이 문서에서는 Synapse SQL을 사용 하 여 T-sql 변수를 할당 하기 위한 팁을 찾을 수 있습니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: abc0a9f6fa6baefc7cc4b29c84ff179f0851dc30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90028919"
---
# <a name="assign-variables-with-synapse-sql"></a>Synapse SQL을 사용 하 여 변수 할당

이 문서에서는 Synapse SQL을 사용 하 여 T-sql 변수를 할당 하기 위한 팁을 찾을 수 있습니다.

## <a name="set-variables-with-declare"></a>DECLARE을 사용 하 여 변수 설정

Synapse SQL의 변수는 문 또는 문을 사용 하 여 설정 됩니다 `DECLARE` `SET` . DECLARE을 사용 하 여 변수를 초기화 하는 것은 Synapse SQL에서 변수 값을 설정 하는 가장 유연한 방법 중 하나입니다.

```sql
DECLARE @v  int = 0
;
```

한 번에 둘 이상의 변수를 설정하려면 DECLARE를 사용할 수도 있습니다. SELECT 또는 UPDATE를 사용 하 여 다음 작업을 수행할 수 없습니다.

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

동일한 DECLARE 문에서 변수를 초기화 하 고 사용할 수 없습니다. 예를 들어, * \@ p1* 이 모두 초기화 되어 동일한 DECLARE 문에서 사용 되므로 다음 예는 허용 되지 않습니다. 다음 예제에서는 오류가 발생합니다.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>집합을 사용 하 여 값 설정

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

변수 할당에는 UPDATE를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [SYNAPSE SQL development 개요](develop-overview.md) 문서를 참조 하세요.
