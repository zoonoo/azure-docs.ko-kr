---
title: 저장 프로시저 사용
description: Azure Synapse Analytics의 전용 SQL 풀에 대한 저장 프로시저를 구현하여 솔루션을 개발하기 위한 팁입니다.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e28eeac131c737d673cac947a3fda30239180a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673589"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀에 대한 저장 프로시저 사용

이 문서에서는 저장 프로시저를 구현하여 전용 SQL 풀 솔루션을 개발하기 위한 팁을 제공합니다.

## <a name="what-to-expect"></a>필요한 항목

전용 SQL 풀은 SQL Server에서 사용되는 여러 가지 T-SQL 기능을 지원합니다. 무엇보다도 솔루션의 성능을 최대화하기 위해 사용할 수 있는 특정 기능 확장 사항이 있습니다.

또한 전용 SQL 풀의 규모와 성능을 유지하기 위해 동작에 차이가 있는 추가 기능이 있습니다.

## <a name="introducing-stored-procedures"></a>저장된 프로시저 소개

저장 프로시저는 전용 SQL 풀 데이터 가까이에 저장되는 SQL 코드를 캡슐화하는 좋은 방법입니다. 저장 프로시저를 사용하면 개발자가 관리 가능한 단위로 코드를 캡슐화하여 솔루션을 모듈화하므로 코드의 재사용 가능성이 크게 촉진됩니다. 각 저장된 프로시저로 매개 변수를 더 유연하게 할 수도 있습니다.

전용 SQL 풀은 단순하고 효율적인 저장 프로시저 구현을 제공합니다. SQL Server와 비교하여 가장 큰 차이점은 저장 프로시저가 미리 컴파일된 코드가 아니라는 점입니다.

일반적으로 데이터 웨어하우스에서 컴파일 시간은 대용량 데이터에 대해 쿼리를 실행하는 데 걸리는 시간에 비해 작습니다. 저장 프로시저 코드가 대형 쿼리에 적절하게 최적화되었는지 확인하는 것은 더욱 중요합니다.

> [!TIP]
> 시간, 분 및 초(밀리초가 아닌)를 저장하는 것이 목표입니다. 그러므로 저장 프로시저를 SQL 논리에 대한 컨테이너로 생각하는 것이 도움이 됩니다.

전용 SQL 풀이 저장 프로시저를 실행할 때 SQL 문은 구문 분석되고 변환되고 런타임 시 최적화됩니다. 이 프로세스 중 각 명령문이 분산 쿼리로 변환됩니다. 데이터에 대해 실행되는 SQL 코드는 전송된 쿼리와 다릅니다.

## <a name="nesting-stored-procedures"></a>중첩 저장 프로시저

저장 프로시저가 다른 저장 프로시저를 호출하거나 동적 SQL을 실행하면 내부 저장 프로시저 또는 코드 호출을 중첩된 것이라고 합니다.

전용 SQL 풀은 최대 8개의 중첩 수준을 지원합니다. 반면 SQL Server의 중첩 수준은 32입니다.

상위 수준 저장 프로시저 호출은 중첩 수준 1과 같습니다.

```sql
EXEC prc_nesting
```

또한 저장 프로시저가 다른 EXEC 호출을 만드는 경우 중첩 수준이 2로 증가합니다.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

그 다음 두 번째 프로시저가 일부 동적 SQL을 실행하면 중첩 수준이 3으로 증가합니다.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

전용 SQL 풀은 현재 [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)을 지원하지 않습니다. 따라서 중첩 수준을 추적해야 합니다. 8개의 중첩 수준 제한을 초과할 가능성은 거의 없습니다. 그러나 초과한다면 이 제한 내에서 중첩 수준에 맞도록 코드 작업을 다시 수행해야 합니다.

## <a name="insertexecute"></a>INSERT..EXECUTE

프로비전된 전용 SQL 풀은 INSERT 문을 사용한 저장 프로시저의 결과 집합을 사용하도록 허용하지 않습니다. 그러나 대체 방법을 사용할 수 있습니다. 예를 들어 [임시 테이블](sql-data-warehouse-tables-temporary.md)의 문서를 참조하세요.

## <a name="limitations"></a>제한 사항

Transact-SQL 저장 프로시저의 측면 중 전용 SQL 풀에서 구현되지 않는 측면은 다음과 같습니다.

* 임시 저장 프로시저
* 숫자가 매겨진 저장 프로시저
* 확장된 저장 프로시저
* CLR 저장 프로시저
* 암호화 옵션
* 복제 옵션
* 테이블 반환 매개 변수
* 읽기 전용 매개 변수
* 기본 매개 변수
* 실행 컨텍스트
* return 문

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
