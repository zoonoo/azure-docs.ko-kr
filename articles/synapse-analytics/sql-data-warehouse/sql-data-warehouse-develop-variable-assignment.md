---
title: 변수 할당
description: 이 문서에서는 Azure Synapse Analytics에서 전용 SQL 풀에 대한 T-SQL 변수를 할당하기 위한 필수 팁을 찾을 수 있습니다.
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
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459226"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀에 대한 변수 할당

이 문서에서는 전용 SQL 풀에서 T-SQL 변수를 할당하기 위한 필수 팁을 찾습니다.

## <a name="set-variables-with-declare"></a>DECLARE를 사용하여 변수 설정

전용 SQL 풀의 변수는 `DECLARE` 문 또는 `SET` 문을 사용하여 설정 됩니다. DECLARE를 사용한 변수 초기화는 SQL 풀의 변수 값을 설정하는 가장 유연한 방법 중 하나입니다.

```sql
DECLARE @v  int = 0
;
```

한 번에 둘 이상의 변수를 설정하려면 DECLARE를 사용할 수도 있습니다. 다음 작업은 SELECT 또는 UPDATE를 사용하여 수행할 수 없습니다.

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

초기화하여 동일한 DECLARE 문에서 변수를 사용할 수 없습니다. 지점을 설명하기 위해 다음 예에서는 @p1이 동일한 DECLARE 문에서 시작되고 사용되기 때문에 허용되지 **않습니다**. 이에 따라 다음 예제에서는 관련 오류를 제공합니다.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>SET을 사용하여 값 설정

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

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
