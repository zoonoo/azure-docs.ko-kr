---
title: 저장 프로시저 사용
description: Synapse SQL 풀에서 저장 프로시저를 구현 하 여 솔루션을 개발 하기 위한 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 87c7eaa57f9da87bd83f89953afc09632d42b1f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213400"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Synapse SQL 풀에서 저장 프로시저 사용

이 문서에서는 저장 프로시저를 구현 하 여 SQL 풀 솔루션을 개발 하기 위한 팁을 제공 합니다.

## <a name="what-to-expect"></a>예상 프로그램

SQL 풀은 SQL Server에서 사용 되는 다양 한 T-sql 기능을 지원 합니다. 무엇보다도 솔루션의 성능을 최대화하기 위해 사용할 수 있는 특정 기능 확장 사항이 있습니다.

또한 SQL 풀의 규모와 성능을 유지 하기 위해 동작 차이가 있는 추가 기능 및 기능이 있습니다.

## <a name="introducing-stored-procedures"></a>저장된 프로시저 소개

저장 프로시저는 sql 풀 데이터에 가까운 SQL 코드를 캡슐화 하는 좋은 방법입니다. 또한 저장 프로시저를 통해 개발자는 코드를 관리 가능한 단위로 캡슐화 하 여 코드를 보다 효율적으로 재사용 가능 하 게 해 주는 솔루션을 모듈화 수 있습니다. 각 저장된 프로시저로 매개 변수를 더 유연하게 할 수도 있습니다.

SQL 풀은 간소화 되 고 간소화 된 저장 프로시저 구현을 제공 합니다. SQL Server에 비해 가장 큰 차이점은 저장 프로시저는 미리 컴파일된 코드가 아님을 나타내는 것입니다.

일반적으로 데이터 웨어하우스의 경우 컴파일 시간은 대용량 데이터 볼륨에 대해 쿼리를 실행 하는 데 걸리는 시간과 비교 하 여 작습니다. 저장 프로시저 코드가 큰 쿼리에 대해 올바르게 최적화 되었는지 확인 하는 것이 더 중요 합니다.

> [!TIP]
> 시간, 분 및 초(밀리초가 아닌)를 저장하는 것이 목표입니다. 따라서 저장 프로시저를 SQL 논리의 컨테이너 라고 생각 하면 유용 합니다.

SQL 풀에서 저장 프로시저를 실행할 때 SQL 문은 런타임에 구문 분석, 변환 및 최적화 됩니다. 이 프로세스 중 각 명령문이 분산 쿼리로 변환됩니다. 데이터에 대해 실행되는 SQL 코드는 전송된 쿼리와 다릅니다.

## <a name="nesting-stored-procedures"></a>중첩 저장 프로시저

저장 프로시저가 다른 저장 프로시저를 호출하거나 동적 SQL을 실행하면 내부 저장 프로시저 또는 코드 호출을 중첩된 것이라고 합니다.

SQL 풀은 최대 8 개의 중첩 수준을 지원 합니다. 반면 SQL Server의 중첩 수준은 32입니다.

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

SQL 풀은 현재 [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 지원 하지 않습니다. 따라서 중첩 수준을 추적 해야 합니다. 8 개의 중첩 수준 제한을 초과할 가능성이 거의 없습니다. 그러나 이렇게 하면이 한도 내에서 중첩 수준에 맞게 코드를 재작업 해야 합니다.

## <a name="insertexecute"></a>INSERT..EXECUTE

SQL 풀에서는 INSERT 문을 사용 하 여 저장 프로시저의 결과 집합을 사용할 수 없습니다. 그러나 사용할 수 있는 대체 방법이 있습니다. 예를 들어 [임시 테이블](sql-data-warehouse-tables-temporary.md)의 문서를 참조하세요.

## <a name="limitations"></a>제한 사항

SQL 풀에서 구현 되지 않는 Transact-sql 저장 프로시저의 몇 가지 측면은 다음과 같습니다.

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
